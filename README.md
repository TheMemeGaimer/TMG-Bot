# TMG

TMG è un bot Discord di moderazione con una dashboard web collegata per
gestirlo senza usare comandi. Il progetto è pensato per essere mostrato
pubblicamente: codice pulito, diviso per responsabilità, pronto per GitHub.

## Architettura

```
┌─────────────┐        gateway Discord        ┌──────────────┐
│   Discord    │ ─────────────────────────────▶│  bot/ (Node) │
│  (server ed  │◀───────────────────────────── │  discord.js  │
│   utenti)    │                                │  + SQLite    │
└─────────────┘                                └──────┬───────┘
       ▲                                               │ API REST interna
       │ OAuth2 login                                  │ (protetta da API key)
       │                                               ▼
┌─────────────────────────────────────────────────────────────┐
│  web/ (Next.js) — landing page + dashboard                   │
│  login "Accedi con Discord" → mostra i server dell'utente    │
│  → configura canale log, benvenuto, ruolo mute               │
└─────────────────────────────────────────────────────────────┘
```

- **bot/** — bot Discord (discord.js v14). Gestisce i comandi slash, il
  sistema di log e salva le impostazioni in un database SQLite locale.
  Espone anche una piccola API REST interna (Express) su cui il sito legge
  e scrive le impostazioni.
- **web/** — sito Next.js con landing page pubblica e dashboard privata.
  Il login usa OAuth2 di Discord (via NextAuth); la dashboard mostra solo i
  server dove l'utente ha il permesso "Gestisci server".

Bot e sito sono due processi separati che comunicano tramite l'API interna:
puoi tenerli sulla stessa macchina o su due host diversi, basta che
`BOT_API_URL` nel sito punti al bot.

## Funzionalità del bot

| Categoria | Comandi |
|---|---|
| Moderazione | `/ban` `/unban` `/kick` `/timeout` `/untimeout` `/warn` `/warnings` `/purge` |
| Utility | `/ping` `/say` `/embed` `/help` |
| Configurazione | `/settings` `/set-log-channel` `/set-welcome` `/set-mute-role` |

Il bot registra automaticamente nel canale di log: ban, membri entrati/usciti,
messaggi eliminati o modificati.

## Prerequisiti

- Node.js 18 o superiore
- Un account Discord e un'applicazione creata su
  [discord.com/developers/applications](https://discord.com/developers/applications)

## 1. Crea l'applicazione Discord

1. Vai su **Discord Developer Portal → New Application**, chiamala "TMG".
2. Nella sezione **Bot**: crea il bot, copia il **Token** (userai `DISCORD_TOKEN`)
   e attiva l'intent **Server Members** e **Message Content**.
3. Nella sezione **OAuth2 → General**: copia **Client ID** e **Client Secret**.
4. Sempre in **OAuth2 → General**, aggiungi questo redirect URL:
   `http://localhost:3000/api/auth/callback/discord`
   (in produzione, sostituisci `localhost:3000` con il tuo dominio).

## 2. Avvia il bot

```bash
cd bot
cp .env.example .env
# apri .env e incolla DISCORD_TOKEN, DISCORD_CLIENT_ID, e scegli API_SECRET

npm install
npm run deploy-commands   # registra gli slash command su Discord
npm start                 # avvia il bot + l'API interna
```

Se imposti `DEV_GUILD_ID` nel `.env` (l'ID di un tuo server di test), i
comandi diventano disponibili all'istante lì; senza, la registrazione è
globale e Discord ci mette fino a un'ora a propagarla su tutti i server.

## 3. Avvia il sito

```bash
cd web
cp .env.example .env.local
# incolla DISCORD_CLIENT_ID/SECRET, genera NEXTAUTH_SECRET con:
#   openssl rand -base64 32
# BOT_API_SECRET deve combaciare con API_SECRET usato nel bot

npm install
npm run dev
```

Apri `http://localhost:3000`: dalla home puoi aggiungere il bot a un server,
fare login con Discord e gestire le impostazioni dalla dashboard.

## Permessi del bot

Il link d'invito richiede: gestione ban/kick, timeout (moderate members),
gestione messaggi, gestione canali e ruoli, lettura/scrittura messaggi. Il
valore numerico è già calcolato in `NEXT_PUBLIC_BOT_PERMISSIONS`
(`.env.example` del sito); puoi ricalcolarlo con il
[permission calculator](https://discordapi.com/permissions.html) ufficiale
se vuoi restringerlo o ampliarlo.

## Struttura del codice

```
TMG/
├── bot/
│   └── src/
│       ├── commands/{moderation,utility,config}/  → uno slash command per file
│       ├── events/                                → un evento Discord per file
│       ├── handlers/                               → caricano comandi/eventi
│       ├── utils/                                   → database, log, embed
│       ├── api/server.js                            → API REST interna
│       └── index.js                                 → avvio del bot
└── web/
    ├── app/
    │   ├── page.js                       → landing page
    │   ├── dashboard/                    → lista server + pagina impostazioni
    │   └── api/                          → auth NextAuth + proxy verso il bot
    ├── components/                        → UI (Navbar, form, card server...)
    └── lib/                                → helper OAuth Discord e chiamate API
```

## Pubblicarlo su GitHub

Il repository è già inizializzato con Git e un primo commit. Per pubblicarlo:

```bash
# crea prima un repository vuoto su github.com (senza README), poi:
git remote add origin https://github.com/TUO-UTENTE/TMG.git
git branch -M main
git push -u origin main
```

## Licenza

MIT — vedi [LICENSE](./LICENSE).
