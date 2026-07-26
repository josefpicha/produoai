# 09 — Inovační AI moduly (výzkumná varianta pro dotaci)

Připraveno pro: **ProduoAI**

Tento dokument popisuje AI funkce v podobě **vlastního výzkumu a vývoje** — variantu vhodnou jako podklad pro dotační podporu (např. program TWIST). Pro samotné MVP doporučujeme realizaci z ověřených hotových řešení (dok. 09b), která je rychlejší a levnější. Obě varianty stojí na stejném datovém základu, takže lze mezi nimi přejít bez přestavby.

## 1. Kontext dotace TWIST

TWIST je program Ministerstva průmyslu a obchodu na výzkum a vývoj v oblasti umělé inteligence. Orientačně: dotace až 30 mil. Kč na projekt, pokrytí až 70 % uznatelných nákladů (až 80 % při spolupráci s výzkumnou organizací). Nejlépe jsou hodnoceny projekty, které AI reálně posouvají — přinášejí řešení překonávající běžně dostupné nástroje. Očekávaným výsledkem je software.

## 2. Princip: oddělení výzkumné části

Pro dotaci je nutné jasně oddělit „běžný vývoj" od „výzkumu", protože dotace financuje pouze výzkumnou část. AI funkce by proto běžely jako samostatné služby s vlastní evidencí experimentů, datovými sadami a měřením přínosu oproti běžným řešením.

## 3. Čtyři inovační moduly (výzkumná podoba)

- **Chytré párování** — model, který se učí z výsledků minulých zakázek a optimalizuje na úspěšně dokončenou zakázku, s cílem měřitelně překonat běžné řazení.
- **Trust Score** — vlastní vysvětlitelný model důvěryhodnosti odolný vůči manipulaci (např. falešné recenze).
- **AI podpora escrow** — model automaticky posuzující splnění kroku a předpovídající riziko sporu (peníze nadále pohybuje platební partner).
- **Analytická vrstva** — vlastní modely pro předpověď poptávky, doporučení cen a sledování výkonu marketplace.

## 4. Návrh ML algoritmů a výzkumných otázek (co bychom ověřovali)

U každého modulu uvádíme výzkumnou otázku, výchozí stav („baseline"), který chceme překonat, kandidátní algoritmy/modely k ověření, způsob měření úspěchu a v čem spočívá novost. Zkratky metod krátce vysvětlujeme.

### 4.1 Chytré párování — naučené řazení (learning-to-rank)
- **Výzkumná otázka:** Dokáže model naučený z výsledků minulých zakázek — optimalizovaný na *úspěšně dokončenou a schválenou zakázku* — překonat prosté řazení podle podobnosti nebo pevných pravidel?
- **Baseline:** podobnost „otisků" (embeddingů) + vážený vzorec (varianta z dok. 09b).
- **Kandidátní modely:**
  - **LambdaMART** (řadicí model nad gradient-boosted stromy, LightGBM/XGBoost) — učí se řadit přímo na kvalitu pořadí.
  - **Two-tower / dual-encoder** (dva enkodéry: poptávka a tvůrce) pro rychlé vyhledání kandidátů + **cross-encoder** pro přesné dořazení.
  - **Doučené embeddingy** (kontrastivní učení na našich datech) proti hotovým.
  - **Kontextové bandity** (Thompson sampling, LinUCB) — průběžné učení z výsledků a řízený „průzkum", aby dostávali šanci i noví tvůrci.
  - **Grafové modely** (GNN, např. LightGCN) nad grafem klient–tvůrce–zakázka pro kolaborativní signál.
- **Měření úspěchu:** offline metriky kvality řazení (NDCG@k, MAP, MRR) na historii; online A/B na konverzi „doporučení → dokončená a schválená zakázka", čas do najmutí, rozmanitost a férovost výběru.
- **Novost:** optimalizace na skutečný výsledek zakázky (ne na podobnost či proklik), férovost (omezení efektu „úspěšní stále úspěšnější") a řešení startu bez historie (cold-start).

### 4.2 Trust Score — kalibrované, vysvětlitelné a manipulaci odolné skóre
- **Výzkumná otázka:** Dokáže model lépe předpovídat reálný výsledek (dokončení bez sporu, opakovaná spolupráce) než vážený vzorec, a přitom zůstat vysvětlitelný, kalibrovaný a férový?
- **Baseline:** vážený vzorec (dok. 09b).
- **Kandidátní modely:**
  - **Gradient boosting** (LightGBM/XGBoost) + **SHAP** (metoda, která u každého skóre ukáže příspěvek jednotlivých faktorů).
  - **Explainable Boosting Machine (EBM)** — model, který je sám o sobě interpretovatelný (vhodné pro regulované skórování osob).
  - **Kalibrace** (Platt scaling / isotonická regrese) — aby skóre odpovídalo pravděpodobnosti dobrého výsledku.
  - **Bayesovská agregace hodnocení** (Beta-Binomial, hierarchické modely) — spravedlivé skóre i u tvůrců s málo hodnoceními.
  - **Časové vážení** (novější chování má větší váhu).
  - **Detekce manipulace:** Isolation Forest / LOF (hledání odlehlých vzorců recenzí), grafová detekce domluvených skupin (komunitní detekce / GNN), NLP klasifikátor pravosti recenzí, detekce nárazových vln hodnocení.
- **Měření úspěchu:** predikční přesnost (AUC, Brierovo skóre) pro bezsporové dokončení a opakovanou spolupráci; kalibrační křivky; férovost napříč segmenty; odolnost při simulovaném útoku falešnými recenzemi.
- **Novost:** spojení kalibrovaného, interpretovatelného skóre s odolností proti manipulaci a vysvětlitelností na úrovni požadavků AI Actu.

### 4.3 AI podpora escrow — ověření splnění milníku a predikce sporu
- **Výzkumná otázka:** Dokáže AI posoudit splnění kroku vůči zadání a včas předpovědět riziko sporu natolik spolehlivě, aby člověku pomáhala (nikoli ho nahrazovala)?
- **Kandidátní modely:**
  - **Porovnání výstupu se zadáním:** jazykový model (Azure OpenAI) + extrakce požadavků + **NLI** (natural language inference — kontrola, zda je každý požadavek ze zadání splněn); u videa/obrazu **multimodální kontrola** (CLIP-style embeddingy, modely porozumění videu).
  - **Predikce sporu:** gradient boosting nad rysy z časové osy a komunikace (latence odpovědí, trend nálady, počet revizí) jako baseline; **sekvenční modely** (transformer/LSTM nad záznamem událostí) pokročile; **analýza přežití** (Coxův model) pro odhad času do sporu.
  - **Detekce konfliktu** v komunikaci (doučený transformer nebo jazykový model).
- **Měření úspěchu:** přesnost a úplnost detekce splnění oproti člověku; předstih a přesnost včasného varování před sporem; míra shody s rozhodnutím člověka.
- **Novost:** multimodální ověření výstupu vůči zadání u kreativní práce a včasná predikce sporu, vždy s člověkem ve smyčce.

### 4.4 Analytická vrstva — cena, poptávka, doporučení
- **Výzkumná otázka:** Dokážeme doporučovat ceny a předpovídat poptávku lépe než prostá historická statistika, a tím zvýšit konverzi a obrat?
- **Kandidátní modely:**
  - **Doporučení ceny:** kvantilová regrese (gradient boosting) pro cenová pásma; hedonická cenová regrese; odhad **cenové elasticity** kauzálními metodami (double machine learning, uplift modeling) → cena maximalizující přijetí × hodnotu.
  - **Předpověď poptávky:** klasika (SARIMA, ETS) jako baseline; **Prophet**; moderní globální modely (**StatsForecast**; neuronové **N-BEATS**, **NHITS**, **Temporal Fusion Transformer**) napříč kategoriemi s hierarchickým sladěním.
  - **Predikce odchodu tvůrců (churn):** analýza přežití / gradient boosting.
  - **Doporučení pro tvůrce** (jaké služby přidat): maticová faktorizace / implicitní zpětná vazba (ALS), sekvenční doporučovače.
  - **Kauzální inference** pro intervence (které pobídky zvyšují dokončení zakázek).
- **Měření úspěchu:** chyba předpovědi (MAE, MAPE, pinball loss) proti baseline; dopad doporučené ceny na přijetí a obrat (A/B); platnost odhadu elasticity.
- **Novost:** kauzální optimalizace ceny a hierarchická vícekategoriová předpověď poptávky přizpůsobená marketplace kreativních služeb.

### 4.5 Společná výzkumná metodika
U každého modulu postupujeme stejně: definujeme baseline, proti němu měříme navržený model, vyhodnocujeme **offline i online (A/B)**, vedeme **evidenci experimentů** (MLflow) a **verzované datové sady**, a hlídáme **férovost a vysvětlitelnost**. Tím zároveň naplňujeme grantový požadavek „překonat známá řešení" i soulad s AI Actem.

## 5. Náročnost

Výzkumná varianta vyžaduje sběr dat, trénování a vyhodnocování modelů, výzkumnou dokumentaci a průběžné prokazování přínosu — je tedy podstatně náročnější než varianta z hotových řešení.

## 6. Doporučení

Pro rychlé a spolehlivé MVP doporučujeme variantu z hotových řešení (dok. 09b). K výzkumné variantě (a případné dotaci) se lze vrátit později, ideálně se zapojením výzkumné organizace. Datový model je na tento přechod připraven.
