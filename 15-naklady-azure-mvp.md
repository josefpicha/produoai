# 15 — Náklady na Azure a další služby (MVP)

> **Jak to číst.** Čísla jsou **orientační měsíční odhady** pro **MVP s malým provozem** (jednotky až nižší tisíce uživatelů, do ~2 mil. požadavků/měsíc) v EU regionu (West Europe / Germany West Central), v režimu pay-as-you-go. Ceny jsou z ceníku k červenci 2026 a je potřeba je **potvrdit v Azure Pricing Calculator** — liší se podle regionu, objemu a nastavení. Uvádím v EUR jako rozpětí.

## 1. Co ceny hlavně žene

- **Container Apps** mají štědrý **free grant** (prvních 180 000 vCPU-sekund, 360 000 GiB-sekund a 2 mil. požadavků měsíčně zdarma) a umí **spadnout na nulu**, když se nepoužívají. Malý provoz je proto levný. Placené sazby jsou ~0,000024 $/vCPU-s a ~0,000003 $/GiB-s; jedna malá kopie (0,5 vCPU / 1 GiB) běžící celý měsíc vyjde asi na ~13 $.
- **PostgreSQL** je největší stálá položka. Výpočetní tier General Purpose se 2 jádry stojí řádově ~125 $/měsíc compute; **vysoká dostupnost (HA)** zhruba **zdvojnásobí** cenu compute. Pro DEV/TEST stačí levný **Burstable** tier se zapínáním/vypínáním.
- **Entra External ID** má **prvních 50 000 aktivních uživatelů měsíčně zdarma** → pro MVP prakticky 0 (ověřit).

## 2. PROD (ostrý provoz, MVP)

| Služba | Konfigurace | EUR/měsíc |
|--------|-------------|-----------|
| Container Apps (api, web, workers, AI) | ~2 vCPU / 4 GiB aktivně, po free grantu | 100–170 |
| PostgreSQL Flexible (General Purpose, 2 jádra) + úložiště + zálohy | bez HA | 130–190 |
| — příplatek za HA (zóna-redundantní) | zdvojení compute | +110–150 |
| Azure Cache for Redis (Standard C1) | | 55–75 |
| Service Bus (Standard) | | 10–20 |
| Blob Storage | ~100–300 GB + operace | 10–30 |
| Front Door (Standard) | základ + provoz | 35–55 |
| Entra External ID | do 50 000 uživatelů zdarma | 0 |
| Azure OpenAI | embeddingy + lehké LLM | 20–100 |
| Communication Services (e-mail) | | 5–20 |
| Web PubSub | free tier / 1 jednotka | 0–45 |
| Container Registry (Standard) | | ~18 |
| Key Vault | | 1–5 |
| Monitor + App Insights + Log Analytics | dle objemu logů | 25–80 |
| Defender for Storage | | 10–30 |
| Přenosy dat (egress) | | 10–30 |

**PROD součet:**
- **bez HA databáze:** ~**440–770 EUR/měsíc** (pravděpodobně kolem 600)
- **s HA databází:** ~**550–920 EUR/měsíc** (pravděpodobně kolem 750–800)

## 3. TEST / staging (zmenšené)

Menší instance, bez HA, Burstable databáze, Container Apps spadnou na nulu, když se netestuje.

| Skupina | EUR/měsíc |
|---------|-----------|
| Container Apps (scale-to-zero) | 20–60 |
| PostgreSQL (Burstable) | 40–90 |
| Redis + Service Bus + Blob + ostatní | 40–90 |
| Monitoring | 15–40 |
| **TEST součet** | **~150–300** |

## 4. DEV (vývoj)

Nejlevnější — vše malé, databáze Burstable se zapínáním/vypínáním mimo pracovní dobu, Container Apps na nule.

| Skupina | EUR/měsíc |
|---------|-----------|
| Container Apps (scale-to-zero) | 10–40 |
| PostgreSQL (Burstable, stop/start) | 25–70 |
| Redis + ostatní | 30–60 |
| **DEV součet** | **~80–180** |

## 5. Celkem Azure (všechna tři prostředí)

| Scénář | EUR/měsíc |
|--------|-----------|
| Bez HA databáze v PROD | **~670–1 250** |
| S HA databází v PROD | **~780–1 400** |

Reálně se dá čekat provoz kolem **900–1 100 EUR/měsíc** pro rozumně nastavené MVP se zálohovanou (HA) produkční databází.

## 6. Náklady mimo Azure

| Položka | Kolik | Poznámka |
|---------|-------|----------|
| **Stripe** | ~1,5 % + 0,25 € za platbu kartou (EU) + poplatky Connect (~2 € měsíčně za aktivní účet tvůrce + poplatek za výplatu) | **není fixní** — je to % z obratu; při obratu 10 000 €/měsíc řádově 150–250 € |
| **Fakturoid / iDoklad** | ~10–30 €/měsíc | fakturace |
| Doména + e-mailová doména | ~2–10 €/měsíc | |

## 7. Jak náklady snížit (páky)

- **Rezervované instance databáze** (závazek na 1 rok) sníží compute Postgresu zhruba o **38 %** — jakmile je provoz předvídatelný, vyplatí se.
- **Scale-to-zero** v DEV/TEST a **stop/start** databáze mimo pracovní dobu.
- **Right-sizing** podle metrik z monitoringu — nezačínat větší, než je potřeba (jde kdykoli zvětšit za pár vteřin).
- **Free granty** (Container Apps, Entra do 50k uživatelů) pokryjí velkou část malého provozu.

> Přesná položková kalkulace pro DEV/TEST/PROD bude připravena po potvrzení regionu a základních velikostí instancí (přes Azure Pricing Calculator).
