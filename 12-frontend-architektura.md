# 12 — Architektura frontendu (routy, obrazovky, volání API, struktura kódu)

Frontend je **Next.js (App Router) + React + TypeScript + Tailwind + shadcn/ui**. Vzhledem k tomu, že návrh UI zatím není k dispozici, níže **navrhujeme obrazovky** podle očekávané funkcionality. **Slouží jako výchozí kostra k doladění s designérem.**

## 1. Tři druhy stránek

1. **Veřejné (pro Google):** vykreslené na serveru (SSR/ISR), aby je našly vyhledávače — katalog, profily tvůrců, landing.
2. **Přihlášené (aplikace):** nástěnky klienta, tvůrce a adminu — běží v prohlížeči, data z API.
3. **Přihlašování:** obrazovky pro registraci/login (řeší Entra External ID, my jen nasměrujeme a zpracujeme návrat).

## 2. Mapa rout (URL) a k čemu slouží

### Veřejné
| Routa | Obrazovka | Klíčová volání API |
|-------|-----------|--------------------|
| `/` | landing | — (statické) |
| `/services` | katalog služeb + filtry (kategorie, cena, hodnocení) | `GET /catalog/services` |
| `/services/[slug]` | detail služby (varianty, cena, ukázky) | `GET /catalog/services/{id}` |
| `/creators/[slug]` | veřejný profil tvůrce (portfolio, hodnocení, Trust Score) | `GET /creators/{id}` |
| `/categories/[slug]` | výpis služeb v kategorii | `GET /catalog/services?category=` |
| `/how-it-works`, `/pricing` | informační stránky | — |

### Přihlašování
| Routa | Obrazovka |
|-------|-----------|
| `/login`, `/register` | přesměrování na Entra a zpracování návratu |
| `/onboarding/creator` | dokončení profilu tvůrce + spuštění ověření přes Stripe |

### Klient (po přihlášení)
| Routa | Obrazovka | Klíčová volání API |
|-------|-----------|--------------------|
| `/client` | nástěnka (aktivní zakázky, poptávky, upozornění) | `GET /orders?role=client`, `GET /notifications` |
| `/client/requests/new` | formulář nové poptávky | `POST /requests` |
| `/client/requests/[id]` | detail poptávky + došlé nabídky + **doporučení tvůrců** | `GET /requests/{id}`, `GET /requests/{id}/matches` |
| `/client/orders/[id]` | průběh zakázky (stav, kroky, soubory, chat, platba) | `GET /orders/{id}`, `POST /orders/{id}/pay`, `POST /orders/{id}/approve` |
| `/client/billing` | historie plateb a faktury | `GET /billing/invoices` |

### Tvůrce (po přihlášení)
| Routa | Obrazovka | Klíčová volání API |
|-------|-----------|--------------------|
| `/creator` | nástěnka (nové poptávky, moje zakázky, výdělky, Trust Score) | `GET /orders?role=creator`, `GET /creators/me/score` |
| `/creator/services` | správa mých služeb a variant | `GET/POST/PATCH /catalog/services` |
| `/creator/portfolio` | správa ukázek | `GET/POST /portfolio` |
| `/creator/requests` | vhodné poptávky (doporučené) | `GET /requests/matched` |
| `/creator/requests/[id]/quote` | poslat nabídku | `POST /quotes` |
| `/creator/orders/[id]` | práce na zakázce (nahrát výstupy, chat) | `GET /orders/{id}`, `POST /orders/{id}/deliverables` |
| `/creator/subscription` | předplatné + uložená karta | `GET /billing/subscription`, `POST /billing/subscription` |
| `/creator/payouts` | přehled výplat | `GET /billing/payouts` |

### Administrátor
| Routa | Obrazovka | Klíčová volání API |
|-------|-----------|--------------------|
| `/admin` | přehled (metriky, fronta ke schválení) | `GET /admin/metrics` |
| `/admin/users` | správa a moderace uživatelů | `GET/PATCH /admin/users` |
| `/admin/orders` | přehled a zásah do zakázek | `GET /admin/orders` |
| `/admin/disputes` | řešení sporů (fáze 2) | `GET/PATCH /admin/disputes` |

## 3. Klíčové akce z pohledu obrazovky (co se posílá a co se vrací)

Aby bylo jasné, co daná obrazovka reálně dělá. Plné specifikace endpointů jsou v dok. 13.

**Nová poptávka** — `/client/requests/new` → `POST /requests`
- **Posílá:** název, brief (zadání), kategorie, rozpočet, měna, termín.
- **Vrací:** vytvořenou poptávku (`id`, `status=published`). Na pozadí se spustí výpočet „otisku" zadání a příprava doporučení (viz párování).
- **Pak:** obrazovka přesměruje na `/client/requests/[id]`, kde se dotáhnou nabídky a doporučení tvůrců.

**Doporučení tvůrců** — `/client/requests/[id]` → `GET /requests/{id}/matches`
- **Vrací:** seřazený seznam tvůrců s **relevancí a vysvětlením** („proč zrovna tenhle"). Zobrazí se jako karty s možností oslovit.

**Poslat nabídku** — `/creator/requests/[id]/quote` → `POST /quotes`
- **Posílá:** `request_id`, cena, měna, dodací lhůta (dny), text nabídky.
- **Vrací:** vytvořenou nabídku (`status=sent`); klientovi přijde upozornění.

**Zaplatit zakázku** — `/client/orders/[id]` → `POST /orders/{id}/pay`
- **Posílá:** (nic navíc) — backend založí platbu ve Stripe a vrátí `client_secret` pro potvrzení karty v prohlížeči.
- **Vrací:** údaje pro dokončení platby; po potvrzení se zakázka posune do stavu `PROBIHA` (fáze 2: peníze se podrží — escrow, dok. 05).

**Nahrát výstup** — `/creator/orders/[id]` → `POST /orders/{id}/deliverables`
- **Posílá:** název výstupu + odkazy na nahrané soubory (nahrané přes dočasný odkaz do Blobu).
- **Vrací:** vytvořený výstup (`status=delivered`); klientovi přijde upozornění ke schválení.

**Schválit dodání** — `/client/orders/[id]` → `POST /orders/{id}/approve`
- **Posílá:** (nic navíc) nebo ID schvalovaného kroku.
- **Vrací:** aktualizovanou zakázku; spustí se **výplata tvůrci minus provize** (dok. 05) a přepočet Trust Score (dok. 16).

## 4. Jak frontend mluví s API

- **Typový klient z popisu API:** backend (FastAPI) vydává popis (OpenAPI); z něj si vygenerujeme klienta v TypeScriptu → volání API jsou „typově hlídaná" a při změně API se chyba pozná hned.
- **Načítání a cache dat:** knihovna **TanStack Query** (drží data, řeší načítání, opětovné dotažení, chybové stavy).
- **Přihlášení:** token z Entra se přikládá ke každému volání; přístup k chráněným routám hlídá middleware Next.js.
- **Nahrávání souborů:** frontend si vyžádá **dočasný odkaz** z backendu (`POST /files/upload-url`) a soubor pošle rovnou do Blobu.
- **Živá upozornění:** připojení na **Web PubSub** (změna stavu zakázky, nová nabídka, nová zpráva).

## 5. Struktura kódu (Next.js App Router)

```
apps/web/
├─ app/
│  ├─ (public)/                # veřejné SEO stránky
│  │  ├─ page.tsx              # landing
│  │  ├─ services/
│  │  ├─ creators/[slug]/
│  │  └─ categories/[slug]/
│  ├─ (auth)/                  # login, register, onboarding
│  ├─ client/                  # sekce klienta (chráněná)
│  ├─ creator/                 # sekce tvůrce (chráněná)
│  ├─ admin/                   # sekce admin (chráněná)
│  └─ layout.tsx
├─ components/                 # sdílené UI komponenty (shadcn/ui)
│  ├─ ui/                      # tlačítka, formuláře, tabulky…
│  ├─ orders/                  # komponenty kolem zakázky
│  ├─ catalog/                 # karty služeb, filtry
│  └─ billing/                 # platby, předplatné
├─ features/                   # logika po doménách (hooky, dotazy)
│  ├─ orders/                  # useOrders, useOrder, mutace
│  ├─ catalog/
│  ├─ billing/
│  └─ notifications/
├─ lib/
│  ├─ api/                     # vygenerovaný typový klient z OpenAPI
│  ├─ auth/                    # práce s přihlášením (Entra)
│  └─ realtime/                # připojení na Web PubSub
├─ middleware.ts               # ochrana chráněných rout
└─ styles/
```

**Zásady:**
- **`components/`** = jak věci vypadají (bez logiky), **`features/`** = jak věci fungují (dotazy na API, stav).
- **Jeden zdroj typů** — vše z OpenAPI klienta, žádné „ručně opsané" typy z backendu.
- **Rozdělení podle domén** (orders, catalog, billing…) kopíruje moduly backendu → snadná orientace.

## 6. Návrh obrazovek (co v UI očekáváme) — pro designéra

- **Detail zakázky (klient i tvůrce):** nahoře stav a kroky (milníky) jako časová osa; uprostřed soubory (zadání, podklady, výstupy) a chat; vpravo akce podle stavu (zaplatit / dodat / schválit / požádat o úpravu).
- **Nová poptávka:** jednoduchý formulář (název, brief, kategorie, rozpočet, termín) + náhled, koho by to mohlo zajímat.
- **Detail poptávky (klient):** došlé nabídky vedle sebe k porovnání + sekce „doporučení tvůrci" s vysvětlením proč (z párování).
- **Profil tvůrce (veřejný):** portfolio, hodnocení, Trust Score s rozpadem faktorů, tlačítko „poptat".
- **Nástěnka tvůrce:** vhodné poptávky, běžící zakázky, výdělky, stav předplatného, Trust Score.
- **Admin přehled:** metriky (počty, obrat, konverze) + fronta věcí ke schválení (moderace, spory).
