# 14 — Repozitáře (kolik jich bude a jak budou vypadat)

## 1. Doporučení: jeden hlavní monorepo + oddělené AI

Pro tým 3 lidí (2 full-stack + 1 ML) je nejrozumnější **monorepo** (jeden git repozitář, uvnitř víc aplikací). Méně přepínání, sdílené věci na jednom místě, jednodušší nasazování. **AI služby** dáme buď do stejného monorepa jako samostatné aplikace, nebo do vlastního repozitáře — podle toho, jak moc chce ML engineer pracovat odděleně.

| Varianta | Kdy zvolit |
|----------|-----------|
| **A) Vše v jednom monorepu** (web + api + ai + infra) | menší tým, chceme jednoduchost a sdílení — **doporučeno pro MVP** |
| **B) Monorepo pro produkt + zvláštní repo pro AI** | ML engineer chce vlastní tempo, vlastní CI a experimenty odděleně |

Pro variantu A (doporučenou) je struktura tato:

## 2. Struktura monorepa

```
produoai/
├─ apps/
│  ├─ web/                  # frontend (Next.js) — viz dok. 12
│  ├─ api/                  # backend jádro (FastAPI) — viz dok. 13
│  └─ ai/                   # AI služby (párování, trust score, analytika)
│     ├─ matchmaker/
│     ├─ trust_score/
│     └─ intelligence/
├─ packages/                # sdílené věci
│  ├─ api-client/           # typový klient z OpenAPI (používá web)
│  ├─ shared-types/         # sdílené typy/konstanty
│  └─ ui/                   # sdílené UI komponenty (nepovinné)
├─ infra/                   # infrastruktura jako kód (Terraform)
│  ├─ modules/
│  └─ envs/                 # dev / test / prod
├─ .github/workflows/       # CI/CD (GitHub Actions) — viz dok. 03
├─ docs/                    # tato dokumentace
└─ docker-compose.yml       # lokální vývoj (spustí vše najednou)
```

## 3. Proč právě takhle

- **`apps/` = spustitelné aplikace**, `packages/` = knihovny, které aplikace sdílejí. Jasné a běžné rozdělení.
- **`api-client`** se generuje z popisu backendu (OpenAPI) a používá ho `web` → frontend a backend jsou vždy „sladěné" a změny se poznají hned.
- **`infra/`** vedle kódu → prostředí (DEV/TEST/PROD) se dají znovu postavit jedním příkazem a změny infrastruktury projdou stejným schvalováním jako kód.
- **`docker-compose.yml`** spustí lokálně databázi, Redis i aplikace → nový člověk se rozjede za pár minut.

## 4. Jak to nasazujeme

- Jeden repozitář, ale **každá aplikace se nasazuje zvlášť** (web, api, každá AI služba mají vlastní kontejner). CI pozná, co se změnilo, a nasadí jen to.
- Změny databáze (Alembic) běží jako samostatný krok před nasazením backendu (dok. 03).

## 5. Větvení a code review

- Práce v krátkých větvích → **pull request** → automatické testy → **code review** kolegy → merge.
- Hlavní větev je vždy nasaditelná; do produkce se pouští po schválení člověkem.
