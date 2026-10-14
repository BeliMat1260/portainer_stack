# free-games-claimer

Stack che esegue [free-games-claimer-remaster](https://github.com/p-adamiec/free-games-claimer-remaster):
un browser automatico che ogni 12 ore entra negli store e riscatta i giochi gratuiti del
momento, poi manda un riepilogo su Discord. Insieme gira un'istanza dedicata di
[Watchtower](https://github.com/nicholas-fedor/watchtower) che tiene aggiornata l'immagine.

Store attivi: Epic Games, Fab, Prime Gaming, GOG, Ubisoft Connect, AliExpress (check-in
giornaliero delle monete).

## Servizi

| Servizio | Container | Immagine | Ruolo |
| --- | --- | --- | --- |
| `free-games` | `free-games-claimer` | `ghcr.io/p-adamiec/free-games-claimer-remaster:latest` | Bot che riscatta i giochi, con browser visibile via noVNC |
| `watchtower` | `free-games-claimer-watchtower` | `nickfedor/watchtower:latest` | Aggiorna automaticamente le immagini di questo stack |

## Porte, volumi e rete

| Tipo | Host | Container | Note |
| --- | --- | --- | --- |
| Porta | `${WEBUI_PORT_FREE_GAMES_CLAIMER}` (6080) | `7080` | Interfaccia web noVNC |
| Volume | `${FOLDER_FOR_CONFIGS}/free-games-claimer` | `/fgc/data` | Sessioni del browser, cookie, database dei riscatti, screenshot |
| Volume | `/var/run/docker.sock` | `/var/run/docker.sock` | Solo Watchtower, per ricreare i container |
| Rete | `utils-network` (bridge) | | Creata dallo stack come `<nome-stack>_utils-network`, subnet scelta da Docker |

## Variabili d'ambiente

Da inserire su Portainer in *Environment variables* → *Advanced mode*. Il modello completo
è in [`stack.env.example`](stack.env.example). Le variabili obbligatorie, se mancano,
bloccano il deploy con `required variable ... is missing a value`.

| Variabile | Obbligatoria | Esempio | Descrizione |
| --- | --- | --- | --- |
| `TIMEZONE` | sì | `Europe/Rome` | Fuso orario dei container (log, orario di Watchtower) |
| `FOLDER_FOR_CONFIGS` | sì | `/DATA/AppData/media-stack` | Cartella dell'host che contiene i dati degli stack |
| `SERVER_IP` | sì | `192.168.1.90` | IP del server, usato nei link noVNC delle notifiche |
| `WEBUI_PORT_FREE_GAMES_CLAIMER` | sì | `6080` | Porta dell'host per l'interfaccia noVNC |
| `WATCHTOWER_SCHEDULE` | no | `0 0 4 * * *` | Quando controllare gli aggiornamenti (cron a 6 campi: sec min ora giorno mese giorno-settimana). Predefinito: ogni giorno alle 04:00 |
| `NOTIFY` | sì | `https://discord.com/api/webhooks/ID/TOKEN` | URL [Apprise](https://github.com/caronc/apprise/wiki) per le notifiche, il webhook Discord è accettato direttamente |
| `EG_EMAIL` / `EG_PASSWORD` | sì | | Account Epic Games (usato anche per Fab) |
| `PG_EMAIL` / `PG_PASSWORD` | sì | | Account Amazon / Prime Gaming |
| `GOG_EMAIL` / `GOG_PASSWORD` | sì | | Account GOG |
| `UBI_EMAIL` / `UBI_PASSWORD` | sì | | Account Ubisoft Connect |
| `AE_EMAIL` / `AE_PASSWORD` | sì | | Account AliExpress |

Le impostazioni fisse sono scritte nel `docker-compose.yml`:

| Variabile | Valore | Effetto |
| --- | --- | --- |
| `STORES` | `epic,fab,prime,gog,ubisoft,aliexpress` | Store da controllare |
| `SCHEDULER_HOURS` | `12` | Un giro di riscatto ogni 12 ore |
| `RUN_ON_STARTUP` | `true` | Un giro subito all'avvio del container (anche dopo ogni aggiornamento) |
| `SHOW` | `1` | Browser visibile su noVNC, necessario per login e captcha |
| `VNC_LOGIN_TIMEOUT` | `600` | Secondi di attesa per un login, codice 2FA o captcha fatto a mano |
| `NOTIFY_MISSING_BASE` | `false` | Non segnala i DLC di cui non possiedi il gioco base |
| `PG_REDEEM`, `GP_CLAIM_DLC` | | Non lette da questa immagine, si possono rimuovere |

Variabili utili non ancora presenti, da aggiungere nel `docker-compose.yml` se servono:
`VNC_PASSWORD` (password per noVNC, consigliata se la porta è raggiungibile da altri),
`EG_OTP_KEY` / `GOG_OTP_KEY` / `UBI_OTP_KEY` (segreti 2FA per il login automatico),
`DISCORD_WEBHOOK` (integrazione Discord nativa, alternativa a `NOTIFY`). L'elenco completo è
nel [README dell'immagine](https://github.com/p-adamiec/free-games-claimer-remaster#configuration).

## Deploy su Portainer

| Campo | Valore |
| --- | --- |
| Repository URL | `https://github.com/BeliMat1260/portainer_stack` |
| Repository reference | `refs/heads/main` |
| Compose path | `free-games-claimer/docker-compose.yml` |
| Environment variables | contenuto di `stack.env.example` con i valori veri, senza virgolette |

Poi *Deploy the stack*. Le istruzioni generali sono nel [README principale](../README.md).

## Primo avvio e login

1. Apri `http://SERVER_IP:WEBUI_PORT_FREE_GAMES_CLAIMER` (es. `http://192.168.1.90:6080`)
   e connettiti a noVNC: vedi il browser controllato dal bot.
2. Al primo giro il bot prova a entrare con email e password. Se uno store chiede un codice
   via email/SMS, una conferma 2FA o un captcha, completalo a mano nella finestra noVNC
   entro `VNC_LOGIN_TIMEOUT` secondi. Le notifiche Discord contengono il link diretto.
3. Le sessioni restano salvate in `/fgc/data/browser`: i login successivi sono automatici.

## Aggiornamenti

- **Automatici:** Watchtower controlla ogni giorno alle 04:00 se esiste una nuova
  `:latest`. Se sì, scarica l'immagine, ricrea il container con la stessa configurazione e
  cancella la vecchia immagine. Agisce solo sui container con label
  `com.centurylinklabs.watchtower.scope=free-games-claimer`, quindi non tocca altri stack.
  Si aggiorna anche da solo.
- **Manuali:** *Stacks* → stack → *Pull and redeploy* (oppure *Update the stack* con
  *Re-pull image* attivo). `pull_policy: always` garantisce il download dell'ultima versione.

Dopo un aggiornamento il container riparte e, con `RUN_ON_STARTUP=true`, esegue subito un
giro di riscatto.

## Dati e backup

Tutto lo stato è in `${FOLDER_FOR_CONFIGS}/free-games-claimer` sull'host:

- `browser/`: profilo del browser con cookie e sessioni (evita di rifare i login);
- database dei giochi già riscattati e dei codici usati;
- `screenshots/`: screenshot salvati quando qualcosa va storto.

Questa cartella sopravvive a ricreazione del container, aggiornamenti e rimozione dello
stack. Per un backup basta copiarla con lo stack fermo. Cancellarla significa rifare tutti
i login.

## Log e diagnostica

- Log del bot: Portainer → *Containers* → `free-games-claimer` → *Logs*, oppure
  `docker logs -f free-games-claimer`.
- Log degli aggiornamenti: `docker logs free-games-claimer-watchtower`.
- Il container ha un healthcheck: lo stato `unhealthy` indica che il bot o noVNC non
  rispondono.

## Problemi frequenti

| Sintomo | Soluzione |
| --- | --- |
| `required variable SERVER_IP is missing a value` | Aggiungi `SERVER_IP` nelle *Environment variables* dello stack |
| `container name "/free-games-claimer" is already in use` | Elimina il vecchio container da *Containers* e rifai il deploy, i dati restano |
| noVNC non si apre | Controlla che la porta `WEBUI_PORT_FREE_GAMES_CLAIMER` non sia usata da un altro container e che il container sia `healthy` |
| Uno store chiede sempre il login | Fai il login a mano su noVNC una volta, oppure aggiungi il segreto 2FA (`*_OTP_KEY`) |
| Nessuna notifica | Verifica l'URL in `NOTIFY`; il webhook Discord deve essere ancora valido |
