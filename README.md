# TMG

**Il tuo server, sotto controllo.**

TMG è un bot Discord open source per la moderazione, pensato per chi
gestisce una community e non vuole passare le giornate a memorizzare
comandi. Ban, kick, timeout, log automatici e una dashboard web dove
configurare tutto con un clic — niente terminale, niente file da
modificare a mano.

---

### 🛡️ Modera senza pensarci

```
/ban utente:@spammer motivo:"pubblicità non autorizzata"
/timeout utente:@rumoroso minuti:10 motivo:"linguaggio offensivo"
/warn utente:@nuovo motivo:"prima di tutto, leggi le regole 😉"
```

Ogni azione finisce automaticamente nel canale di log, con chi l'ha fatta,
a chi e perché. Nessun ban "misterioso" di cui nessuno sa nulla.

### 🧭 Una dashboard, non un manuale

Aggiungi TMG al server, fai login con Discord, e da lì scegli il canale
dei log, il messaggio di benvenuto e il ruolo mute. Le modifiche sono
immediate — il bot non va riavviato.

### 📜 I log parlano

TMG tiene traccia di:
- ban e sban
- membri che entrano ed escono
- messaggi cancellati o modificati
- ogni comando di moderazione usato, da chi e su chi

---

## Comandi

| | |
|---|---|
| `/ban` `/unban` | Banna e sbanna, con opzione per ripulire i messaggi recenti |
| `/kick` | Espelle un utente dal server |
| `/timeout` `/untimeout` | Silenzia temporaneamente, o toglie il silenzio |
| `/warn` `/warnings` | Registra e consulta gli avvertimenti di un utente |
| `/purge` | Ripulisce in blocco gli ultimi messaggi di un canale |
| `/say` `/embed` | Fa scrivere al bot un messaggio o un embed personalizzato |
| `/settings` | Mostra la configurazione attuale del server |

## 🧱 Sotto il cofano

- **Bot** — Node.js + [discord.js](https://discord.js.org), con un database
  SQLite locale per le impostazioni di ogni server.
- **Dashboard** — Next.js, login con OAuth2 di Discord, parla con il bot
  tramite una piccola API interna.
- Codice diviso per responsabilità: un file per comando, un file per
  evento — facile da leggere, facile da estendere.

## 🚀 In breve

```bash
cd bot && cp .env.example .env    # incolla token e client ID del bot
npm install && npm run deploy-commands && npm start

cd web && cp .env.example .env.local
npm install && npm run dev        # dashboard su localhost:3000
```

Guida completa passo-passo, incluso come creare l'applicazione su Discord,
nella [wiki](#) *(o più giù, se preferisci tenerla nello stesso file)*.

## 🤝 Contribuire

Pull request, issue e idee per nuovi comandi sono benvenute. Se aggiungi
un comando, mettilo nella cartella giusta sotto `bot/src/commands/` e
compare automaticamente sia in `/help` che nella dashboard.

## 📄 Licenza

Distribuito con licenza MIT — vedi [LICENSE](./LICENSE). Usalo, modificalo,
portalo sul tuo server.
