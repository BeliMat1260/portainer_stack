# Giochi gratis

Un browser che, ogni 12 ore, entra negli store e riscatta quello che nel
frattempo è diventato gratuito. Poi manda il riepilogo su Discord. Se uno
store chiede un captcha o un codice, lo finisci tu dalla finestra noVNC.

Il bot è [free-games-claimer-remaster](https://github.com/p-adamiec/free-games-claimer-remaster).
Accanto gira un [Watchtower](https://github.com/nicholas-fedor/watchtower)
che aggiorna solo questo stack.

Store: Epic Games, Fab, Prime Gaming, GOG, Ubisoft Connect, AliExpress.

Su Portainer lo stack si chiama `free_games`.
Compose path: `free-games-claimer/docker-compose.yml`.

## I due container

| Servizio | Container | Ruolo |
| --- | --- | --- |
| `free-games` | `free-games-claimer` | Riscatta i giochi. Il browser si vede via noVNC |
| `watchtower` | `free-games-claimer-watchtower` | Alle 04:00 controlla se c'è una `:latest` nuova e, se sì, ricrea il container |

noVNC è su `http://SERVER_IP:WEBUI_PORT_FREE_GAMES_CLAIMER`
(in casa `http://192.168.1.90:6080`). Sessioni, cookie e giochi già riscattati
stanno in `${FOLDER_FOR_CONFIGS}/free-games-claimer`, montato su `/fgc/data`.

Watchtower vede il socket Docker ed è limitato ai container con label
`com.centurylinklabs.watchtower.scope=free-games-claimer`. Il resto della casa
non lo tocca.

## Variabili

Il modello è [`stack.env.example`](stack.env.example). Se ne manca una
obbligatoria, il deploy si ferma con `required variable ... is missing a value`.

| Variabile | Obbligatoria | A cosa serve |
| --- | --- | --- |
| `TIMEZONE` | sì | Fuso di log e di Watchtower. `Europe/Rome` |
| `FOLDER_FOR_CONFIGS` | sì | Cartella dati sull'host. `/DATA/AppData/media-stack` |
| `SERVER_IP` | sì | IP messo nei link noVNC delle notifiche |
| `WEBUI_PORT_FREE_GAMES_CLAIMER` | sì | Porta noVNC. `6080` |
| `NOTIFY` | sì | URL [Apprise](https://github.com/caronc/apprise/wiki). Un webhook Discord va bene così com'è |
| `EG_EMAIL` / `EG_PASSWORD` | sì | Epic Games, usato anche per Fab |
| `PG_EMAIL` / `PG_PASSWORD` | sì | Amazon / Prime Gaming |
| `GOG_EMAIL` / `GOG_PASSWORD` | sì | GOG |
| `UBI_EMAIL` / `UBI_PASSWORD` | sì | Ubisoft Connect |
| `AE_EMAIL` / `AE_PASSWORD` | sì | AliExpress |
| `WATCHTOWER_SCHEDULE` | no | Cron a 6 campi. Vuoto significa ogni giorno alle 04:00 (`0 0 4 * * *`) |

Nel compose, fissi:

| Valore | Effetto |
| --- | --- |
| `STORES=epic,fab,prime,gog,ubisoft,aliexpress` | Store controllati |
| `SCHEDULER_HOURS=12` | Un giro ogni 12 ore |
| `RUN_ON_STARTUP=true` | Un giro anche a ogni avvio, quindi anche dopo un aggiornamento |
| `SHOW=1` | Browser visibile, serve per captcha e login |
| `VNC_LOGIN_TIMEOUT=600` | Secondi per completare a mano un codice o un captcha |
| `NOTIFY_MISSING_BASE=false` | Niente avviso per i DLC di cui manca il gioco base |

Se la porta noVNC è raggiungibile da altri, vale la pena aggiungere
`VNC_PASSWORD`. I segreti 2FA (`EG_OTP_KEY`, `GOG_OTP_KEY`, `UBI_OTP_KEY`) si
aggiungono nel compose quando uno store li chiede ogni volta. L'elenco completo
è nel [README dell'immagine](https://github.com/p-adamiec/free-games-claimer-remaster#configuration).

## La prima volta

1. Apri noVNC e connettiti. Vedi il browser del bot.
2. Al primo giro prova le email e le password. Se lo store chiede un codice,
   una conferma o un captcha, completalo in quella finestra entro
   `VNC_LOGIN_TIMEOUT` secondi. Discord manda il link diretto.
3. Le sessioni restano in `/fgc/data/browser`. I giri dopo entrano da soli.

## Aggiornamenti e backup

Watchtower, ogni giorno alle 04:00, scarica la `:latest` se è cambiata, ricrea
il container con gli stessi volumi e cancella l'immagine vecchia. A mano:
*Pull and redeploy*. `pull_policy: always` scarica comunque l'ultima versione.

Dopo l'aggiornamento parte subito un giro, per via di `RUN_ON_STARTUP=true`.

Nella cartella dati: `browser/` (cookie e sessioni), il database dei riscatti,
`screenshots/` quando qualcosa va storto. Per un backup, ferma lo stack e copia
la cartella. Cancellarla significa rifare tutti i login.

I log del bot sono sul container `free-games-claimer`, quelli degli update su
`free-games-claimer-watchtower`. Lo stato `unhealthy` dice che il bot o noVNC
non rispondono.

## Se qualcosa non parte

| Sintomo | Cosa fare |
| --- | --- |
| `required variable SERVER_IP is missing a value` | Aggiungi `SERVER_IP` nelle variabili dello stack |
| `container name "/free-games-claimer" is already in use` | Elimina il container vecchio. I dati nella cartella restano |
| noVNC non si apre | La porta è libera e il container è `healthy` |
| Uno store chiede sempre il login | Fallo una volta da noVNC, oppure aggiungi `*_OTP_KEY` |
| Nessuna notifica | L'URL in `NOTIFY` deve essere ancora valido |
