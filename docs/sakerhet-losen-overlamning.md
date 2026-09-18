# Teknisk överlämning: lösenordsrotation och säkerhetsgenomgång inför main

**Ansvarig:** Alfredo  
**Gren/PR:** [cursor/lesson-memory-tool-544c](https://github.com/barrettaalfredo-hue/v1-central-context-base-for-LLMS/tree/cursor/lesson-memory-tool-544c) · [PR #30](https://github.com/barrettaalfredo-hue/v1-central-context-base-for-LLMS/pull/30)  
**Uppgift:** Ta bort testlösenord ur git-ytan och kontrollera att inga nycklar ligger i branchen inför merge av `integration/v1.1` till `main`  
**Granskad:** 18 september 2026, commit `daec985`

## Vad jag har gjort

De tre testlösenorden hade tidigare legat i README (git-historik). De ska inte följa med till `main`.

Alfredo har bytt lösenorden i Supabase. **De nya lösenorden ligger i Confluence-dokumentet Setup V1.1.** De ligger inte i git.

I koden är README uppdaterad: inloggning står i Confluence och lösenordshanteraren. Inga lösenord, tokens eller API-nycklar ska committas. Kontona är desamma (Filip, Alfredo, Melker). Inga användare är raderade, så minnen och MCP-kopplingar behåller samma `user_id`.

## Hur fungerar lösningen?

Samma tre konton i Supabase Auth (`v1-central-context-base`, Stockholm). Dashboarden har ingen ruta för att skriva in nytt lösenord, bara “Send password recovery” som skickar mejl. Testdresserna `@example.com` tar inte emot mejl, så lösenordet byts i SQL Editor mot `auth.users` (bcrypt). Det syns inte i appen. Appen läser bara det nya lösenordet vid nästa inloggning.

Git-filen [docs/v1.1-setup.md](v1.1-setup.md) beskriver hur man ansluter Claude, ChatGPT och Grok. Själva lösenorden står bara i Confluence Setup V1.1.

## Så kan ni testa

1. Öppna Confluence **Setup V1.1** och hämta det nya lösenordet där. Inte från git, inte från gamla README.
2. Logga in på V1.1-dashboarden med `alfredo.test@example.com` och det nya lösenordet. Förväntat: inloggning lyckas.
3. Samma för Filip och Melker.
4. Gammalt lösenord ska ge `INVALID_CREDENTIALS`.
5. Claude/ChatGPT/Grok: logga ut och in igen mot samma MCP-adress. Samma konto som dashboarden.
6. Minnen som fanns före bytet ska fortfarande synas för samma person.

## Hur har det verifierats?

**Säkerhetsgranskning (`/review-security`):** kördes mot branchen jämfört med `integration/v1.1`. Verktyget kunde inte räkna ut branchen-diffen (samma fel två gånger), så den automatiska rapporten blev inte klar.

**Manuell genomgång av nuvarande filer (18 september 2026):**

- Inga de tre gamla testlösenorden i arbetskopian.
- Inga `service_role`-värden, inga `.env.local` i git.
- README och `docs/v1.1-setup.md` pekar på Confluence/lösenordshanterare, inte på hemliga värden.
- Publika anon-nyckeln finns som fallback i `apps/api/lib/supabase/env.ts` (roll `anon`, inte admin).
- Filips mock-lösen `mock-losen` finns kvar för lokalt dashboard-läge, inte för live Supabase.

**Inte verifierat här:** att de tre nya lösenorden i Setup V1.1 faktiskt fungerar mot live-inloggning. Det gör Alfredo i stegen ovan.

## Vad behöver andra veta?

De gamla lösenorden finns kvar i git-historiken (README-commit från 12 september). Historiken rensas inte utan omskrivning av git. Därför är rotationen nödvändig. Använd bara de nya i Setup V1.1.

Klistra inte in de nya lösenorden i git, PR eller chattloggar.

Merga till `integration/v1.1`, inte till `main`, förrän teamet sagt att V1.1 får gå live.
