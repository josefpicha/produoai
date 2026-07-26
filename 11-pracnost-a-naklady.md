# 11 — Pracnost, harmonogram a náklady

> Pracnost je uvedena v člověko-dnech (MD) jako rozpětí (od–do) — jde o kvalifikovaný odhad, který se zpřesní po doladění rozsahu. Cena vývoje je vypočtena při denní sazbě 7 000 Kč/MD (bez DPH); konečná fakturace probíhá dle skutečně odpracovaných MD. Provozní náklady (Azure aj.) uvádíme konkrétně.

## 1. Pracnost po etapách

| Etapa | Rozsah | Pracnost (MD) |
|-------|--------|---------------|
| E0 | Příprava a architektura | 24–39 |
| E1 | MVP jádro | 99–159 |
| E2 | Platby a průběh zakázky | 84–134 |
| E3 | Doladění na produkci | 47–77 |
| **Produkční MVP (E0–E3)** | | **254–409** |
| E4 | Escrow a strana klienta | 46–76 |
| E5 | AI funkce (varianta z hotových řešení) | 54–89 |
| **Celkem (E0–E5)** | | **354–574** |

## 2. Harmonogram (realizační tým 3 lidé: 2 full-stack + 1 ML)

Předpoklady: dva full-stack vývojáři na jádru, jeden ML inženýr na AI funkcích. AI je zpočátku odložitelná, takže ML inženýr v úvodu vypomáhá s vývojem jádra a na AI se plně přesune později — AI tak běží souběžně a přidává jen krátký závěr.

| Etapa | Přibližně kalendářně |
|-------|----------------------|
| E0 Příprava a architektura | 3–5 týdnů |
| E1 MVP jádro | 2–3,5 měsíce |
| E2 Platby a průběh | 2–3 měsíce |
| E3 Doladění na produkci | 1,5–2,5 měsíce |
| **→ Produkční MVP (E0–E3)** | **~6–10 měsíců** |
| E4 Escrow a strana klienta | 1,5–2,5 měsíce |
| E5 AI funkce (souběžně) | +1–2 měsíce navíc |
| **→ Plné MVP vč. escrow a AI** | **~9–13 měsíců** |

Harmonogram zkrátí užší rozsah MVP, hotový návrh UI a zkušenější tým; prodlouží jej složitější integrace (DPH, escrow) a změny zadání.

## 3. Cena vývoje

Vypočteno při denní sazbě **7 000 Kč / MD** (ceny bez DPH).

| Etapa | Pracnost (MD) | Cena (Kč) |
|-------|---------------|-----------|
| E0 Příprava a architektura | 24–39 | 168 000 – 273 000 |
| E1 MVP jádro | 99–159 | 693 000 – 1 113 000 |
| E2 Platby a průběh | 84–134 | 588 000 – 938 000 |
| E3 Doladění na produkci | 47–77 | 329 000 – 539 000 |
| **Produkční MVP (E0–E3)** | **254–409** | **1 778 000 – 2 863 000** |
| E4 Escrow a strana klienta | 46–76 | 322 000 – 532 000 |
| E5 AI funkce | 54–89 | 378 000 – 623 000 |
| **Plné MVP (E0–E5)** | **354–574** | **2 478 000 – 4 018 000** |

**Doporučená rezerva +15–25 %** (nejistota, integrace, právní náležitosti):

| Rozsah | Cena vč. rezervy (Kč) |
|--------|------------------------|
| Produkční MVP (E0–E3) | ~2 045 000 – 3 579 000 |
| Plné MVP (E0–E5) | ~2 850 000 – 5 023 000 |

Ceny jsou uvedeny bez DPH. Jde o odhad na základě rozsahu; konečná fakturace probíhá dle skutečně odpracovaných MD.

## 4. Provozní náklady (měsíčně)

Detailní položkový rozpis je v dok. 15.

- **Azure** (všechna tři prostředí DEV/TEST/PROD): ~**670–1 400 EUR/měsíc**; reálně kolem 900–1 100 EUR se zálohovanou (HA) produkční databází.
- **Stripe:** ~1,5 % + 0,25 € za platbu kartou (není fixní — jde o podíl z obratu).
- **Ostatní:** fakturace (~10–30 €/měsíc), doména; jednorázově penetrační test (2 000–6 000 €) a právní/daňové poradenství.

## 5. Body zpřesňující odhad

1. Doladění rozsahu MVP (co odsunout do fáze 2).
2. Fakturace a DPH, cílové trhy.
3. Návrh UI (obrazovky jsou navrženy v dok. 12) — upřesní pracnost frontendu.
