# 06 — Bezpečnost, ochrana dat a soulad s regulací

Právní pojmy a zkratky vysvětlujeme srozumitelně a v obchodních souvislostech.

## 1. Přihlašování a oprávnění

- Přihlašování zajišťuje služba **Entra External ID** (Microsoft, data v EU): registrace, hesla, ověření e-mailu. Ukládáme pouze odkaz na uživatele, nikoli heslo.
- **Dvoufaktorové ověření** je povinné pro administrátory a doporučené pro tvůrce (mají navázané výplaty).
- **Role a oprávnění** (klient / tvůrce / administrátor) se kontrolují na serveru.
- Aplikace se k databázi a klíčům přihlašuje přes spravovanou identitu — bez hesel v kódu.

## 2. Ochrana dat

| Oblast | Opatření |
|--------|----------|
| Přenos | veškerá komunikace šifrovaná (HTTPS) |
| Uložení | data i soubory šifrované |
| Klíče a hesla | v trezoru Key Vault, pravidelná obměna |
| Vstupy | ověřování, ochrana proti běžným útokům |
| Soubory | antivirová kontrola, přístup jen přes dočasné odkazy |
| Databáze | dostupná jen z privátní sítě |
| Vstupní brána | Front Door s ochranou proti útokům (WAF) |

## 3. GDPR — ochrana osobních údajů

GDPR jsou evropská pravidla pro nakládání s osobními údaji. Platforma zpracovává údaje klientů i tvůrců, proto se jimi plně řídíme.

Zabudovaná opatření:

1. **Minimalizace dat** — doklady totožnosti drží Stripe, přihlašovací údaje Entra; my držíme minimum.
2. **Právní důvod zpracování** — plnění zakázky, oprávněný zájem (bezpečnost, prevence podvodů), souhlas (marketing, AI profilování).
3. **Práva uživatelů** — přístup k datům, oprava, přenositelnost a **výmaz**. U finančních dokladů, které je nutné ze zákona uchovat, se výmaz řeší odstraněním osobních údajů (**pseudonymizace**), účetní záznam zůstává.
4. **Doby uchování** — různé kategorie dat (soubory, komunikace, faktury, logy) mají různé lhůty a automatický úklid.
5. **Smlouvy s dodavateli:**
   - **DPA** (smlouva o zpracování osobních údajů) — uzavíráme s každým zpracovatelem (Stripe, Microsoft, e-mail): zavazuje je nakládat s daty bezpečně a dle našich pokynů.
   - **SCC** (vzorové smluvní doložky EU) — pro případný přenos dat mimo EU; cílem je ponechat data v EU.
   - **ROPA** (záznamy o činnostech zpracování) — přehled „jaká data, proč a jak dlouho zpracováváme".
6. **DPIA** (posouzení vlivu na soukromí) — provádí se u citlivějšího zpracování, například u skórování osob (Trust Score).
7. **Ohlášení incidentu** — proces nahlášení případného úniku do 72 hodin.

## 4. AI Act — pravidla pro AI

AI Act je evropský zákon o umělé inteligenci. Funkce Trust Score a párování pracují s hodnocením osob, proto se jich týká.

Aktuální stav (2026):
- Zákaz nejrizikovějších použití platí od února 2025.
- Povinnosti pro „vysoce rizikové" AI byly odloženy na **prosinec 2027**; transparenční povinnosti a registrace platí od srpna 2026.
- Nezávisle platí **GDPR čl. 22** — právo nebýt předmětem čistě automatizovaného rozhodnutí s významným dopadem.

Posouzení pro platformu:
- Párování a Trust Score pravděpodobně nespadají mezi „vysoce rizikové", klasifikaci však formálně provedeme a zdokumentujeme.
- Skóre se počítá výhradně z chování na platformě (kvalita, spolehlivost, rychlost) — je kontextově omezené a obhajitelné.
- Pokud by skóre samo omezovalo tvůrci výdělek, vyžaduje to zásah člověka a možnost odvolání (dle čl. 22).

Zabudovaná opatření (viz i dok. 04 a 16):
1. **Vysvětlitelnost** — u každého skóre je rozpad na faktory.
2. **Dohled člověka** — automatika sama nikoho neblokuje; omezení potvrzuje administrátor.
3. **Možnost odvolání** — uživatel vidí své skóre a může jej nechat přezkoumat.
4. **Správa dat** — trénovací/vstupní data zpracována zákonně a bez skryté diskriminace.
5. **Verzování a záznamy** — u každého výstupu se ukládá verze výpočtu.
6. **Transparentnost** — uživatelé jsou informováni o zapojení AI.

## 5. Bezpečnostní správa

- Pravidelná obměna klíčů, automatická kontrola zranitelností a bezpečnosti balíčků.
- Oddělený, jen ke čtení určený záznam finančních a bezpečnostních akcí s delší dobou uchování.
- Citlivé akce (výplaty, výmazy) potvrzuje více osob.
- **Penetrační test** před spuštěním a poté pravidelně.
- Volitelně soulad s normou **ISO/IEC 42001** (řízení AI) — vhodné i pro případnou dotaci.
