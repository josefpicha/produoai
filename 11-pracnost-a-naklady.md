# 11 — Pracnost, harmonogram a náklady

> Pracnost je uvedena v člověko-dnech (MD) jako rozpětí (od–do). Odhad **předpokládá vývoj s kódovacím agentem** (Cursor / Claude Code), který zvyšuje produktivitu. Přínos je odstupňovaný podle typu práce: u rutinnějšího vývoje počítáme se **snížením o 50 %**, u fází s vyšším podílem návrhu integrací a modelů — **platby (E2)** a **AI funkce (E5)** — konzervativněji se **snížením o 35 %**. Cena vývoje je vypočtena při denní sazbě 7 000 Kč/MD (bez DPH); konečná fakturace probíhá dle skutečně odpracovaných MD. Provozní náklady (Azure aj.) uvádíme konkrétně.

## 1. Pracnost po etapách

| Etapa | Rozsah | Přínos agenta | Pracnost (MD) |
|-------|--------|---------------|---------------|
| E0 | Příprava a architektura | −50 % | 12–20 |
| E1 | MVP jádro | −50 % | 50–80 |
| E2 | Platby a průběh zakázky | −35 % | 55–87 |
| E3 | Doladění na produkci | −50 % | 24–39 |
| **Produkční MVP (E0–E3)** | | | **141–226** |
| E4 | Escrow a strana klienta | −50 % | 23–38 |
| E5 | AI funkce (varianta z hotových řešení) | −35 % | 35–58 |
| **Celkem (E0–E5)** | | | **199–322** |

## 2. Harmonogram (realizační tým 3 lidé: 2 full-stack + 1 ML, s kódovacím agentem)

Předpoklady: dva full-stack vývojáři na jádru, jeden ML inženýr na AI funkcích, vývoj podpořený kódovacím agentem. AI je zpočátku odložitelná, takže ML inženýr v úvodu vypomáhá s vývojem jádra a na AI se plně přesune později — AI tak běží souběžně a přidává jen krátký závěr.

| Etapa | Přibližně kalendářně |
|-------|----------------------|
| E0 Příprava a architektura | 2–3 týdny |
| E1 MVP jádro | 1–1,5 měsíce |
| E2 Platby a průběh | 1,5–2 měsíce |
| E3 Doladění na produkci | ~1 měsíc |
| **→ Produkční MVP (E0–E3)** | **~3,5–5,5 měsíce** |
| E4 Escrow a strana klienta | ~1 měsíc |
| E5 AI funkce (souběžně) | +~1 měsíc navíc |
| **→ Plné MVP vč. escrow a AI** | **~5,5–7,5 měsíce** |

Harmonogram zkrátí užší rozsah MVP a hotový návrh UI; prodlouží jej složitější integrace (DPH, escrow) a změny zadání.

## 3. Cena vývoje

Vypočteno při denní sazbě **7 000 Kč / MD** (ceny bez DPH).

| Etapa | Pracnost (MD) | Cena (Kč) |
|-------|---------------|-----------|
| E0 Příprava a architektura | 12–20 | 84 000 – 140 000 |
| E1 MVP jádro | 50–80 | 350 000 – 560 000 |
| E2 Platby a průběh | 55–87 | 385 000 – 609 000 |
| E3 Doladění na produkci | 24–39 | 168 000 – 273 000 |
| **Produkční MVP (E0–E3)** | **141–226** | **987 000 – 1 582 000** |
| E4 Escrow a strana klienta | 23–38 | 161 000 – 266 000 |
| E5 AI funkce | 35–58 | 245 000 – 406 000 |
| **Plné MVP (E0–E5)** | **199–322** | **1 393 000 – 2 254 000** |

**Doporučená rezerva +15–25 %** (nejistota, integrace, právní náležitosti):

| Rozsah | Cena vč. rezervy (Kč) |
|--------|------------------------|
| Produkční MVP (E0–E3) | ~1 135 000 – 1 978 000 |
| Plné MVP (E0–E5) | ~1 602 000 – 2 818 000 |

Ceny jsou bez DPH. Jde o odhad na základě rozsahu a předpokladu vývoje s kódovacím agentem; konečná fakturace probíhá dle skutečně odpracovaných MD.

## 4. Provozní náklady (měsíčně)

Detailní položkový rozpis je v dok. 15.

- **Azure** (všechna tři prostředí DEV/TEST/PROD): ~**670–1 400 EUR/měsíc**; reálně kolem 900–1 100 EUR se zálohovanou (HA) produkční databází.
- **Stripe:** ~1,5 % + 0,25 € za platbu kartou (není fixní — jde o podíl z obratu).
- **Ostatní:** fakturace (~10–30 €/měsíc), doména; jednorázově penetrační test (2 000–6 000 €) a právní/daňové poradenství.
- **Nástroje vývoje:** licence kódovacího agenta (Cursor / Claude Code) pro tým — řádově desítky EUR na vývojáře měsíčně.

## 5. Body zpřesňující odhad

1. Doladění rozsahu MVP.
2. Fakturace a DPH, cílové trhy.
3. Návrh UI (obrazovky jsou navrženy v dok. 12) — upřesní pracnost frontendu.
