# Giochi gratis

Ogni 12 ore un browser entra negli store, riscatta i giochi gratuiti e manda il riepilogo. Captcha e codici si completano da noVNC. Il programma è [free-games-claimer-remaster](https://github.com/p-adamiec/free-games-claimer-remaster).

Store: Epic Games, Fab, Prime Gaming, GOG, Ubisoft Connect, AliExpress.

| | |
| --- | --- |
| Nome su Portainer | `free_games` |
| Compose | [docker-compose.yml](docker-compose.yml) |
| Variabili | [stack.env.example](stack.env.example) |
| noVNC | `http://<SERVER_IP>:6080` |

Il compose non dichiara `name:`, quindi su Portainer lo stack si chiama `free_games`. [Installazione](../README.md#installazione).

## Servizi

| Servizio | Container | Ruolo |
| --- | --- | --- |
| `free-games` | `free-games-claimer` | Riscatta i giochi. Il browser è su noVNC |
| `watchtower` | `free-games-claimer-watchtower` | Alle 04:00, se `:latest` è cambiata, ricrea il container |

Watchtower guarda solo i container con scope `free-games-claimer`. L'orario è `WATCHTOWER_SCHEDULE` (`0 0 4 * * *`).

## Dati

Sessioni, cookie e riscatti stanno in `/DATA/AppData/media-stack/free-games-claimer`, montato su `/fgc/data`: profilo del browser, database dei giochi, screenshot.

Nel compose sono fissi gli store, l'intervallo di 12 ore, un giro anche all'avvio, il browser visibile e 600 secondi per completare a mano un login o un captcha. Le notifiche usano un URL [Apprise](https://github.com/caronc/apprise/wiki). Nell'esempio l'URL è un webhook Discord.
