# 02 — Technologický stack

## 1. Přehled

| Vrstva | Technologie | Zdůvodnění |
|--------|-------------|------------|
| Frontend | Next.js (React + TypeScript) | dohledatelnost ve vyhledávačích (veřejný katalog a profily), moderní ekosystém |
| Vzhled | Tailwind CSS + shadcn/ui | rychlé a přístupné hotové komponenty |
| Backend | Python + FastAPI | jednotný jazyk s AI funkcemi, výkon, automatický popis API |
| Práce s databází | SQLAlchemy + Alembic | spolehlivé čtení/zápis a řízené změny struktury |
| Databáze | PostgreSQL + pgvector | jedna databáze pro data i vyhledávání podobnosti (párování) |
| Fronta a cache | Redis + ARQ | úlohy na pozadí a zrychlení opakovaných dotazů |
| Události | Azure Service Bus | spolehlivé předávání zpráv mezi částmi |
| Soubory | Azure Blob Storage | úložiště zadání, podkladů a výstupů |
| Přihlašování | Microsoft Entra External ID | hotová služba pro registraci a přihlášení, data v EU |
| Platby | Stripe (Connect + Billing) | platby, výplaty tvůrcům, předplatné |
| E-maily | Azure Communication Services | transakční e-maily, data v EU |
| Živá upozornění | Azure Web PubSub | okamžitá upozornění v aplikaci |
| AI funkce | Azure OpenAI | hotové AI modely s daty v EU |
| Běh aplikace | Azure Container Apps | automatické škálování, úspora v klidových obdobích |
| Prostředí (kód) | Terraform | reprodukovatelná prostředí DEV/TEST/PROD |
| Nasazování | GitHub Actions | automatické testy a nasazení |
| Sledování | Azure Monitor + Application Insights | logy, chyby, výkon na jednom místě |
| Tajné údaje | Azure Key Vault | bezpečné uložení klíčů |

## 2. Frontend — Next.js

Dohledatelnost je pro marketplace zásadní. Veřejné profily a katalog musí být rychlé a indexovatelné vyhledávači. Next.js vykresluje veřejné stránky na serveru (vhodné pro vyhledávače) a přihlášenou část v prohlížeči, v jednotném ekosystému React + TypeScript.

## 3. Backend — FastAPI

Jednotný jazyk (Python) pro jádro i AI funkce, dobrý výkon při komunikaci s externími službami a automatický popis API, z něhož lze generovat typově hlídaného klienta pro frontend (méně integračních chyb).

## 4. Databáze — PostgreSQL

Jedna databáze pokrývá běžná data i vyhledávání podobnosti (rozšíření **pgvector**), pružná pole (JSON) a základní fulltextové vyhledávání v katalogu. Samostatnou vyhledávací službu (Azure AI Search) zvážíme až při výrazném růstu.

## 5. Otevřené body

- **Přihlašování:** doporučujeme Microsoft Entra External ID (konzistentní s Azure, data v EU).
- **Fronta na pozadí:** doporučujeme ARQ pro MVP.
- **Nasazování prostředí:** doporučujeme Terraform (přenositelnost napříč cloudy).
