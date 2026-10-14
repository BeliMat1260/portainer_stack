# Giochi gratis

Browser automatico che ogni 12 ore riscatta i giochi gratuiti e manda il
riepilogo su Discord. Captcha e codici si completano dalla finestra noVNC.

Il bot è [free-games-claimer-remaster](https://github.com/p-adamiec/free-games-claimer-remaster).
Watchtower aggiorna solo questo stack.

Store: Epic Games, Fab, Prime Gaming, GOG, Ubisoft Connect, AliExpress.

Nome su Portainer: `free_games`.
Compose path: `free-games-claimer/docker-compose.yml`.

## Servizi

| Servizio | Container | Ruolo |
| --- | --- | --- |
| `free-games` | `free-games-claimer` | Riscatta i giochi. Il browser è su noVNC |
| `watchtower` | `free-games-claimer-watchtower` | Alle 04:00 controlla `:latest` e, se è cambiata, ricrea il container |

noVNC: `http://SERVER_IP:WEBUI_PORT_FREE_GAMES_CLAIMER`
(`http://192.168.1.90:6080`).

Dati, cookie e riscatti: `${FOLDER_FOR_CONFIGS}/free-games-claimer` montato su
`/fgc/data`. Dentro ci sono `browser/`, il database dei giochi e `screenshots/`.

Watchtower usa il socket Docker e agisce solo sui container con label
`com.centurylinklabs.watchtower.scope=free-games-claimer`.

## Variabili

Modello: [`stack.env.example`](stack.env.example).

| Variabile | A cosa serve |
| --- | --- |
| `TIMEZONE` | Fuso. `Europe/Rome` |
| `FOLDER_FOR_CONFIGS` | Cartella dati. `/DATA/AppData/media-stack` |
| `SERVER_IP` | IP nei link noVNC delle notifiche |
| `WEBUI_PORT_FREE_GAMES_CLAIMER` | Porta noVNC. `6080` |
| `NOTIFY` | URL [Apprise](https://github.com/caronc/apprise/wiki) o webhook Discord |
| `EG_EMAIL` / `EG_PASSWORD` | Epic Games e Fab |
| `PG_EMAIL` / `PG_PASSWORD` | Prime Gaming |
| `GOG_EMAIL` / `GOG_PASSWORD` | GOG |
| `UBI_EMAIL` / `UBI_PASSWORD` | Ubisoft Connect |
| `AE_EMAIL` / `AE_PASSWORD` | AliExpress |
| `WATCHTOWER_SCHEDULE` | Cron a 6 campi. Vuoto: ogni giorno alle 04:00 |

Impostazioni scritte nel compose: store `epic,fab,prime,gog,ubisoft,aliexpress`,
un giro ogni 12 ore, un giro anche all'avvio, browser visibile, 600 secondi
per un login o un captcha fatto a mano.

All'indirizzo noVNC si vede il browser del bot. Le sessioni restano in
`/fgc/data/browser`.
