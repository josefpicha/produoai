# 08 — Škálovatelnost (jak zvládneme růst)

Tenhle dokument záměrně odkazuje na **konkrétní části naší architektury** (z dok. 01–03), ať je jasné, čím se co řeší.

## 1. O jak velký provoz jde

Zadání říká „tisíce" klientů a tvůrců. To je z pohledu serverů **malé až střední** zatížení. Cíl:
- zvládnout jednotky tisíc uživatelů a stovky souběžných zakázek **bez zbytečného přeplácení**,
- mít **jasnou cestu, jak povyrůst** o řád výš bez přepisu aplikace,
- neplatit dopředu za výkon, který ještě nepotřebujeme.

Zásada: **škálujeme, až když to čísla ukážou**, ne dopředu „pro jistotu".

## 2. Jak škáluje každá část (a čím konkrétně)

| Část naší architektury | Jak se škáluje | Konkrétně u nás |
|------------------------|----------------|-----------------|
| **Frontend (Next.js na Container Apps)** | víc kopií + zrychlení statického obsahu | Container Apps přidá kopie podle počtu požadavků; **Front Door (CDN)** cachuje statické části |
| **Backend (FastAPI na Container Apps)** | automaticky víc kopií podle vytížení | pravidlo autoscalingu Container Apps na počet požadavků / CPU; backend je „bezstavový", takže přidání kopie nic nerozbije |
| **Workers (na pozadí)** | víc kopií podle délky fronty | Container Apps škáluje workery podle **délky fronty v Service Bus** (víc čekajících úkolů → víc workerů) |
| **Databáze (PostgreSQL Flexible Server)** | silnější stroj + čtecí kopie | zvětšíme výpočetní tier; při hodně čtení přidáme **read repliку** a čtení katalogu na ni směrujeme |
| **Spojení k databázi** | sdílení přes prostředníka | **PgBouncer** (viz níže) |
| **Vyhledávání podobnosti** | index v databázi | **pgvector** v té samé Postgres; samostatnou vyhledávací DB (Azure AI Search) až při statisících záznamů |
| **Soubory (Blob Storage)** | prakticky bez limitu | Blob zvládne růst sám; často stahované výstupy přes CDN |
| **AI služby (oddělené Container Apps)** | škálují nezávisle na jádru | přepočty (embeddingy, Trust Score) běží dávkově mimo špičku; výsledky se ukládají a cachují |

## 3. „Prostředník" u databáze = PgBouncer (vysvětleno)

**Problém:** každá kopie backendu i workerů si otevírá spojení k databázi. Když Container Apps za špičky nafoukne třeba 10 kopií a každá drží desítky spojení, PostgreSQL se **zahltí počtem spojení** dřív, než mu dojde výkon. Databáze má totiž strop na počet souběžných spojení.

**Řešení:** mezi aplikaci a databázi dáme **PgBouncer** — to je „prostředník na spojení" (connection pooler). Funguje jako **recepce**: aplikace mluví s PgBouncerem, on drží menší počet skutečných spojení do Postgresu a chytře je mezi požadavky přepíná. Aplikace si tak může otevřít „hodně" spojení, ale databáze jich reálně obslouží málo a v klidu.

**Konkrétně u nás:** Azure Database for PostgreSQL — Flexible Server má **PgBouncer zabudovaný** (stačí ho zapnout), takže nemusíme provozovat nic navíc. Aplikace se připojí přes port PgBounceru.

## 4. Triky, které nám pomůžou (a kde v architektuře jsou)

- **Backend si nic „nepamatuje" mezi požadavky** → stav je v PostgreSQL a v Redisu, ne v paměti kontejneru. Proto můžeme přidávat kopie backendu bez potíží (Container Apps).
- **Cache v Redisu** = dočasné uložení výsledku, ať ho nepočítáme pořád dokola (katalog, profily, výsledky párování s omezenou platností).
- **Co nemusí být hned, jde na pozadí** přes Service Bus + workery (e-maily, přepočet skóre, faktury) → uživatel nečeká.
- **PgBouncer** hlídá spojení k databázi (viz výše).

## 5. Škálování AI a párování (konkrétně)

- **„Otisky" (embeddingy)** počítáme jen **při změně** profilu nebo služby (spustí se přes událost na Service Bus), ne při každém dotazu → uložíme do `embedding` v Postgres (pgvector).
- **Výsledky párování** pro opakované dotazy ukládáme do Redisu (s omezenou platností).
- **Trust Score** počítá worker **dávkově** (např. každou noc) a doplňkově při klíčových událostech — ne při každém načtení stránky.
- pgvector v jedné databázi bez potíží zvládne „tisíce" tvůrců. Samostatnou vyhledávací databázi (Azure AI Search) přidáme, až kdyby jich byly statisíce.

## 6. Cílová kvalita (co budeme měřit v Application Insights)

| Ukazatel | Cíl pro MVP |
|----------|-------------|
| Odezva při čtení (95 % požadavků do) | < 300 ms |
| Odezva při zápisu | < 600 ms |
| Odezva párování | < 800 ms |
| Dostupnost | ≥ 99,5 % |
| Zpracování úkolu na pozadí | < 30 s |

(„95 % požadavků do X ms" znamená, že drtivá většina je rychlá; nekoukáme jen na průměr, který umí schovat výkyvy.) Tyhle hodnoty sledujeme v **Application Insights** (dok. 03) a při překročení přijde upozornění.

## 7. Kdy sáhnout po složitějším řešení

Ne podle kalendáře, ale podle **signálů z monitoringu**:
- jedna část (typicky AI) potřebuje škálovat úplně jinak než zbytek → osamostatníme ji (už teď je oddělená),
- nasazování monolitu začne tým brzdit → zvážíme rozdělení podle nejaktivnějších hranic modulů,
- potřeba GPU nebo složitější orchestrace → přechod z Container Apps na **Kubernetes (AKS)**.

Do té doby modulární monolit + oddělené AI služby + PgBouncer + Redis bohatě stačí.
