# Plán C — Náklady na Azure

Připraveno pro: **ProduoAI**

> Orientační měsíční odhady pro **MVP s malým provozem** v EU regionu, v režimu pay-as-you-go. Ceny (červenec 2026) doporučujeme potvrdit v Azure Pricing Calculator. Uvedeno v EUR jako rozpětí. Plán C zahrnuje **chat** (Web PubSub) a **párování** (Azure OpenAI embeddingy + pgvector); neobsahuje samostatné AI služby (Trust Score, analytika), Service Bus ani platby za zakázky.

## 1. PROD (ostrý provoz)

| Služba | Konfigurace | EUR/měsíc |
|--------|-------------|-----------|
| Container Apps (web, api vč. párování, workery) | ~1,5–2 vCPU aktivně | 70–140 |
| PostgreSQL Flexible (2 jádra) + pgvector + úložiště + zálohy | bez HA | 100–190 |
| — příplatek za HA (zóna-redundantní) | zdvojení compute | +100–140 |
| Azure Cache for Redis (Standard C1) | cache + fronta | 55–75 |
| **Web PubSub (chat)** | 1 jednotka | 40 |
| **Azure OpenAI (embeddingy párování)** | jen embeddingy | 5–30 |
| Blob Storage | přílohy | 10–25 |
| Front Door (Standard) | základ + provoz | 35–55 |
| Entra External ID | do 50 000 uživatelů zdarma | 0 |
| Communication Services (e-mail) | | 5–20 |
| Container Registry (Standard) | | ~18 |
| Key Vault | | 1–5 |
| Monitor + App Insights + Log Analytics | dle objemu logů | 20–60 |
| Defender for Storage (volitelně) | | 10–25 |
| Přenosy dat (egress) | | 10–25 |

**PROD součet:**
- **bez HA databáze:** ~**385–710 EUR/měsíc** (pravděpodobně kolem 500–550)
- **s HA databází:** ~**485–850 EUR/měsíc** (pravděpodobně kolem 650–700)

## 2. TEST / staging (zmenšené)

Menší instance, bez HA, Burstable databáze, Container Apps spadnou na nulu mimo testování; Web PubSub a embeddingy jen při testech.

| Skupina | EUR/měsíc |
|---------|-----------|
| Container Apps (scale-to-zero) | 20–60 |
| PostgreSQL (Burstable) + pgvector | 40–90 |
| Redis + Web PubSub + Blob + ostatní | 50–100 |
| OpenAI (embeddingy, testovací objem) | 2–15 |
| Monitoring | 15–40 |
| **TEST součet** | **~130–300** |

## 3. DEV (vývoj)

| Skupina | EUR/měsíc |
|---------|-----------|
| Container Apps (scale-to-zero) | 10–40 |
| PostgreSQL (Burstable, stop/start) + pgvector | 25–70 |
| Redis + Web PubSub + ostatní | 30–70 |
| OpenAI (embeddingy, malý objem) | 2–10 |
| **DEV součet** | **~90–190** |

## 4. Celkem Azure (všechna tři prostředí)

| Scénář | EUR/měsíc |
|--------|-----------|
| Bez HA databáze v PROD | **~600–1 200** |
| S HA databází v PROD | **~700–1 340** |

Reálně lze čekat provoz kolem **700–900 EUR/měsíc** pro rozumně nastavené MVP se zálohovanou (HA) produkční databází. Oproti plné verzi šetří Plán C hlavně na samostatných AI službách (Trust Score, analytika), Service Busu a na tom, že z Azure OpenAI využívá jen levné embeddingy.

## 5. Náklady mimo Azure

| Položka | Kolik | Poznámka |
|---------|-------|----------|
| **Stripe** | poplatek z předplatného + karetní poplatky | **jen předplatné tvůrců** — žádné poplatky z obratu zakázek |
| Fakturace předplatného (Fakturoid/iDoklad) | ~10–30 €/měsíc | |
| Doména + e-mailová doména | ~2–10 €/měsíc | |
| Licence kódovacího agenta (Cursor / Claude Code) | řádově desítky € / vývojář | |

## 6. Jak náklady snížit

- **Rezervované instance databáze** (závazek na 1 rok) sníží compute Postgresu ~o 38 %.
- **Scale-to-zero** v DEV/TEST, **stop/start** databáze mimo pracovní dobu.
- **Web PubSub** lze v DEV/TEST držet na Free tieru; embeddingy počítat jen při změně (ne opakovaně).
- **Free granty** (Container Apps, Entra do 50k uživatelů) pokryjí velkou část malého provozu.

> Přesná položková kalkulace pro DEV/TEST/PROD bude připravena po potvrzení regionu a velikostí instancí (přes Azure Pricing Calculator).
