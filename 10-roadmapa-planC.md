# Plán C — Roadmapa (~3 měsíce)

Připraveno pro: **ProduoAI**

Rozvržení do fází. Předpokládá tým 3 lidí a vývoj s kódovacím agentem (Cursor / Claude Code).

```mermaid
graph LR
    C0[C0 Příprava<br/>a architektura] --> C1[C1 Účty<br/>a katalog]
    C1 --> C2[C2 Poptávky, párování<br/>a propojení]
    C2 --> C3[C3 Předplatné<br/>a notifikace]
    C3 --> C4[C4 Doladění<br/>a spuštění]
```

## Co Plán C zahrnuje a co ne (rozdíl oproti plné verzi)

| Funkce / schopnost | Plná verze | **Plán C** |
|--------------------|:---------:|:----------:|
| Účty, role, profily | ✔ | ✔ |
| Katalog + veřejné stránky (SEO) | ✔ | ✔ |
| Zadávání poptávek a nabídek | ✔ | ✔ |
| Propojení klient ↔ tvůrce | ✔ | ✔ |
| Nahrávání příloh (brief, portfolio) | ✔ | ✔ |
| Předplatné tvůrců | ✔ | ✔ |
| Notifikace | ✔ | ✔ (základ) |
| Administrace | ✔ | ✔ |
| Hodnocení | ✔ | ✔ (základní) |
| **Platby za zakázky přes platformu** | ✔ | ✖ |
| **Provize z realizovaných zakázek** | ✔ | ✖ |
| **Výplaty tvůrcům (Stripe Connect)** | ✔ | ✖ |
| **Escrow (zádržné)** | ✔ (fáze 2) | ✖ |
| **Milníkový průběh zakázky** | ✔ | ✖ |
| **Odevzdávání výstupů přes platformu** | ✔ | ✖ |
| **Řešení sporů** | ✔ (fáze 2) | ✖ |
| Chytré párování (matching) | ✔ | ✔ |
| Chat klient ↔ tvůrce (real-time) | ✔ | ✔ |
| **analytická vrstva** | ✔ (fáze 3) | ✖ |

> V Plánu C probíhá **realizace i platba za zakázku mimo platformu** (přímo mezi klientem a tvůrcem). Platforma je katalog a nástroj propojení; příjmem je **předplatné tvůrců**.

## C0 — Příprava a architektura
- odsouhlasení rozsahu Plánu C
- příprava cloudu (Azure), prostředí DEV, automatické nasazování

**Hotovo, když:** je schválený rozsah a běží prostředí DEV.

## C1 — Účty a katalog
- registrace/přihlášení (Entra External ID), role klient / tvůrce / administrátor
- profily klienta a tvůrce
- katalog služeb a profilů, veřejné stránky dohledatelné vyhledávači
- jednoduché vyhledávání a filtrování
- nahrávání příloh k profilu / portfoliu

**Hotovo, když:** tvůrci se registrují, mají profil a služby a katalog je veřejně k nalezení.

## C2 — Poptávky, párování a propojení
- zadávání poptávek klienty (popis, kategorie, rozpočet, termín, přílohy)
- reakce/nabídky tvůrců na poptávky
- **chytré párování**: doporučení vhodných tvůrců k poptávce (embeddingy + pgvector)
- **propojení**: klient vybere tvůrce → zahájení komunikace
- **chat** klient ↔ tvůrce (real-time zprávy přes WebSockets)

**Hotovo, když:** proběhne celý tok poptávka → párování/nabídka → propojení a klient s tvůrcem spolu komunikují v chatu. (Realizace a platba dál probíhá mimo platformu.)

## C3 — Předplatné a notifikace
- **předplatné tvůrců** (Stripe Billing): měsíční/roční, uložení karty, fakturace předplatného
- notifikace e-mailem (nová poptávka, nová nabídka, propojení)

**Hotovo, když:** tvůrce si aktivuje předplatné a dostává upozornění na relevantní události.

## C4 — Administrace, doladění a spuštění
- administrace: správa uživatelů, moderace obsahu, přehled předplatných
- základní hodnocení po propojení
- produkční doladění: bezpečnost a GDPR (základ), monitoring, zálohování, prostředí TEST/PROD, nasazování bez výpadku
- dokumentace a příprava na spuštění

**Hotovo, když:** platforma je nasaditelná, zabezpečená, zálohovaná, zdokumentovaná a připravená pro reálné uživatele.

## Fáze vs. kalendář

| Fáze | Měsíc |
|------|-------|
| C0 Příprava a architektura | 1 (start) |
| C1 Účty a katalog | 1 |
| C2 Poptávky, párování a propojení | 2 |
| C3 Předplatné a notifikace | 2 |
| C4 Administrace, doladění a spuštění | 3 |

## Milníky

| Milník | Kritérium |
|--------|-----------|
| M1 — Katalog naživo | registrovaní tvůrci, profily a služby, veřejný katalog |
| M2 — Párování, propojení + předplatné | párování, tok poptávka → nabídka → propojení, chat, aktivní předplatné |
| M3 — Produkční spuštění | doladěné, zabezpečené, zdokumentované, spuštěné |

## Co je mimo Plán C (pro pozdější rozšíření)

Platby za zakázky přes platformu, výplaty tvůrcům, provize, escrow, milníkový průběh zakázky, odevzdávání výstupů přes platformu, řešení sporů a pokročilé AI moduly (analytická vrstva). Chytré párování a chat jsou součástí Plánu C. Architektura i datový model jsou na jejich pozdější doplnění připravené — jde o rozšíření, ne přestavbu.
