# 09b — AI funkce v MVP (z ověřených hotových řešení)

Tento dokument popisuje realizaci AI funkcí pro MVP postavenou na **ověřených hotových nástrojích** — bez trénování vlastních modelů. Přístup je rychlý, nákladově úsporný a od začátku vysvětlitelný. Použité stavební kameny:

- **Azure OpenAI** (hotové modely, data v EU):
  - **embeddingy** — převod textu (např. zadání poptávky) na číselný „otisk", z něhož lze počítat podobnost;
  - **jazykový model** — vytažení struktury z textu nebo krátká shrnutí.
- **pgvector** — rozšíření databáze pro rychlé hledání nejpodobnějších „otisků".
- **Průhledné vážené vzorce** — počítání nad údaji, které již máme (hodnocení, spolehlivost…); každý výsledek je vysvětlitelný.

---

## Funkce 1 — Chytré párování

**Účel:** doporučit klientovi k poptávce nejvhodnější tvůrce (a tvůrci vhodné poptávky).

**Realizace:**
1. Ke každé službě i poptávce se spočítá „otisk" (Azure OpenAI) a uloží do pgvector.
2. Pro poptávku najde pgvector obsahově nejbližší tvůrce.
3. Kandidáti se seřadí **váženým vzorcem** i podle tvrdých údajů (cena, hodnocení, spolehlivost, kapacita) — výsledkem je seřazený seznam s vysvětlením.
4. Volitelně jazykový model vytáhne ze zadání strukturu (dovednosti, rozpočet), pokud ji klient nevyplnil.

**Použitá data:**
- *O tvůrci:* dovednosti a kategorie, texty profilu/služeb/portfolia (pro „otisk"), ceny a dodací lhůty, hodnocení, podíl dokončených a včas dodaných zakázek, rychlost odpovědí, kapacita, historie zakázek.
- *O poptávce:* text zadání, kategorie (pro „otisk"), rozpočet, termín, případně dovednosti vytažené jazykovým modelem.

**Příklad váhového vzorce:** `skóre = 0,45·obsahová shoda + 0,20·hodnocení + 0,15·spolehlivost + 0,10·cena v rozpočtu + 0,10·kapacita`. Každý faktor je viditelný, takže je vždy zřejmé, proč je tvůrce doporučen.

---

## Funkce 2 — Trust Score (skóre důvěryhodnosti)

**Účel:** jedno srozumitelné číslo (např. 0–100) u tvůrce i klienta shrnující spolehlivost.

**Realizace:** průhledný vážený vzorec nad již sbíranými údaji — bez trénovaného modelu. Je tak spravedlivý, vysvětlitelný a odolný vůči náhodným výkyvům.

**Použitá data:**
- *Tvůrce:* průměrné hodnocení a jejich počet, podíl dokončených a včas dodaných zakázek, rychlost odpovědí, podíl sporů, stáří účtu a počet zakázek.
- *Klient:* hodnocení od tvůrců, platební spolehlivost, počet sporů.

**Příklad výpočtu (tvůrce):** `trust = 30·(hodnocení/5) + 25·podíl dokončených + 20·podíl včasných + 15·rychlost odpovědí + 10·(1 − podíl sporů)`. Ukládá se i rozpad na faktory a verze výpočtu.

**Ochrana proti manipulaci:** hodnocení se počítá jen z reálně dokončených a zaplacených zakázek; u nováčků se skóre označí jako „málo dat". Skóre nikoho automaticky neblokuje — případné omezení potvrzuje administrátor (dok. 06).

---

## Funkce 3 — AI podpora escrow

**Účel:** pomoci rozhodnout, zda je krok zakázky hotový a zda uvolnit peníze.

**Realizace:** základem jsou **pravidla** (klient schválí krok → uvolní se odpovídající část), což pokryje MVP i fázi 2. **AI slouží pouze jako nápověda** — jazykový model porovná dodaný výstup se zadáním a připraví doporučení pro člověka; o penězích nerozhoduje.

**Použitá data:** text zadání, popis kroku a výstupů, komunikace u zakázky (pro odhad hrozícího sporu).

---

## Funkce 4 — Analytika a doporučení cen

**Účel:** doporučená cena pro klienta/tvůrce a přehled o výkonu marketplace.

**Realizace:**
- *Doporučení ceny:* statistika nad podobnými minulými zakázkami (medián a rozpětí v dané kategorii).
- *Přehledy:* běžné grafy a souhrny; volitelně krátká slovní shrnutí přes Azure OpenAI.
- *Předpověď poptávky (volitelně):* knihovna **Prophet** (od Mety) — jednoduchá, sama zvládá sezónnost a svátky a dává rozumné výsledky bez ladění.

**Použitá data:** kategorie, ceny a lhůty, částky a výsledky zakázek, hodnocení — vše v souhrnu (agregovaně).

---

## Shrnutí

| Funkce | Hotové řešení | Vlastní model? |
|--------|----------------|----------------|
| Párování | Azure OpenAI embeddingy + pgvector + vážený vzorec | ne |
| Trust Score | průhledný vážený vzorec | ne |
| AI escrow | pravidla + jazykový model jako nápověda | ne |
| Analytika / ceny | statistika + Prophet pro předpovědi | ne |

Vše je vysvětlitelné, úsporné a rychle realizovatelné. Datový model umožňuje případný pozdější přechod na výzkumnou variantu (dok. 09).
