# ProduoAI — Architektura MVP a produkčního řešení

**Návrh softwarové architektury evropského AI marketplace kreativních služeb**
Připraveno pro: **ProduoAI** · Verze: 1.0 · Datum: 2026-07-25

---

## Úvod

Tento dokument a navazující sada dokumentů popisují návrh, jak vybudovat marketplace ProduoAI — platformu propojující **klienty** (poptávající AI videa, weby, UGC obsah a další kreativní služby) s **tvůrci**, kteří tyto služby dodávají.

Řešení stavíme jako **MVP (první produkční verzi)** navrženou od základu, s architekturou připravenou na další fáze (zejména escrow) a na budoucí rozšíření o inovační AI moduly.

Kde používáme odborný termín, vysvětlujeme ho z hlediska byznysového pohledu.

## Struktura dokumentace

| # | Dokument | Obsah |
|---|----------|-------|
| 00 | Přehled a shrnutí (toto README) | rozsah, klíčová rozhodnutí, slovníček |
| 01 | Architektura řešení | z jakých částí se aplikace skládá a jak spolupracují |
| 02 | Technologický stack | zvolené technologie a jejich zdůvodnění |
| 03 | Azure infrastruktura | cloudové služby a mapování na jednotlivé funkce |
| 04 | Datový model | struktura databáze (tabulky, typy, vazby) |
| 05 | Platby a escrow | platby, provize, předplatné a zádržné |
| 06 | Bezpečnost, GDPR a AI Act | ochrana dat a soulad s regulací |
| 07 | Integrace | napojení na Stripe, e-mail, úložiště, AI |
| 08 | Škálovatelnost | připravenost na růst |
| 09 | Inovační moduly (grant) | AI funkce jako výzkumná nadstavba (kvůli TWIST) |
| 09b | AI implementace (MVP) | AI funkce z ověřených hotových řešení (rychlejší vývoj) |
| 10 | Roadmapa | pořadí realizace |
| 11 | Pracnost a náklady | rozsah práce, harmonogram, provozní náklady (dle navrženého MVP) |
| 12 | Frontend | obrazovky, routy, struktura (základní návrh) |
| 13 | Backend | rozhraní (API), propojení s AI, struktura (základní návrh) |
| 14 | Repozitáře | organizace zdrojového kódu |
| 15 | Náklady na Azure | provozní náklady MVP |
| 16 | Architektura AI služeb | vnitřní návrh jednotlivých AI služeb |

## Slovníček

- **MVP** — první produkční verze produktu s nezbytnou funkcionalitou.
- **Frontend** — část aplikace v prohlížeči (uživatelské rozhraní).
- **Backend** — serverová část (logika, databáze, platby).
- **API** — rozhraní, přes které spolu komunikují frontend a backend, případně jednotlivé služby.
- **Modulární monolit** — aplikace nasazovaná jako jeden celek, uvnitř rozdělená na oddělené moduly (katalog, objednávky, platby…).
- **Databáze / PostgreSQL** — úložiště dat (uživatelé, objednávky…). PostgreSQL je konkrétní zvolená databáze.
- **Účetní přehled plateb** — vlastní evidence každého pohybu peněz (platba, provize, výplata) pro přehled a kontrolu vůči platebnímu partnerovi.
- **Událost** — zpráva „stalo se X" (např. „objednávka zaplacena"), na kterou reaguje jiná část systému.
- **Cloud / Azure** — provozní prostředí od Microsoftu, kde aplikace poběží.
- **Stripe** — platební partner zajišťující platby kartou a výplaty tvůrcům.
- **Escrow (zádržné)** — peníze klienta se podrží a tvůrci uvolní po dodání a schválení práce.

## Rozsah po fázích

**Fáze 1 — MVP (nejdříve strana tvůrců)**
Účty a role (klient / tvůrce / administrátor); registrace tvůrce včetně ověření totožnosti; katalog služeb (veřejně dohledatelný); zadávání poptávek a objednávek; průběh zakázky od poptávky po dodání a schválení; nahrávání zadání, podkladů a výstupů; platby přes Stripe (za zakázku, měsíční/roční předplatné tvůrců, provize); fakturace; notifikace; administrace; monitoring, zálohování, automatické nasazování, oddělená prostředí a dokumentace.

**Fáze 2 — Zádržné a plná strana klienta**
Escrow (podržení a uvolnění platby po milnících); řešení sporů; další monetizační modely (např. success fee).

**Fáze 3+ (lze paralelně) — Inovační AI moduly**
Chytré párování, skóre důvěryhodnosti, AI podpora escrow a analytická vrstva. Pro MVP navrhujeme jejich realizaci z ověřených hotových řešení (dok. 09b); plnohodnotná výzkumná varianta (vhodná pro dotaci) je popsána v dok. 09.

## Klíčová architektonická rozhodnutí

| Rozhodnutí | Zvoleno | Detail |
|-----------|---------|--------|
| Styl aplikace | modulární monolit + oddělené AI služby | dok. 01 |
| Cloud | Microsoft Azure, region v EU | dok. 03 |
| Backend | Python + FastAPI | dok. 02 |
| Frontend | Next.js (React + TypeScript) | dok. 02 |
| Databáze | PostgreSQL (+ pgvector) | dok. 04 |
| Platby | Stripe (Stripe Connect) | dok. 05 |
| Escrow | přes Stripe (fáze 2) | dok. 05 |
| Přihlašování | Microsoft Entra External ID | dok. 06 |
| Úložiště souborů | Azure Blob Storage | dok. 07 |
| AI funkce (MVP) | hotová řešení (Azure OpenAI + průhledné vzorce) | dok. 09b |

## Body k odsouhlasení

Následující body doporučujeme uzavřít před zahájením implementace (nejsou překážkou pro dokončení návrhu):

1. Fakturace a DPH u tvůrců, zejména přeshraniční v rámci EU (zapojit daňového poradce).
2. Cílový trh — pro MVP počítáme se střední Evropou v rámci EU.
3. Případný záměr žádat o dotační podporu (ovlivní volbu mezi variantou AI dle dok. 09b a 09).

## Cílový stav

Moderní, bezpečný a škálovatelný evropský AI marketplace provozovaný v EU, s platebním jádrem přes Stripe, připravený na tisíce klientů a tvůrců, s možností rozšíření o inovační AI moduly.
