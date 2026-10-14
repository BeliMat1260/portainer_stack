# Pi-hole

DNS della LAN. Gira sulla rete dell'host per ascoltare la porta `53`. La web
UI sta su `WEBUI_PORT_PIHOLE` (`8081`).

Nome su Portainer: `pihole`.
Compose path: `pihole/docker-compose.yml`.

| Variabile | Valore |
| --- | --- |
| `PIHOLE_PASSWORD` | Password della web UI |
| `PIHOLE_UPSTREAMS` | DNS a monte, `1.1.1.1;9.9.9.9` |
| `TIMEZONE` | `Europe/Rome` |
| `FOLDER_FOR_CONFIGS` | I dati sono in `${FOLDER_FOR_CONFIGS}/pihole` |

Il modello è [`stack.env.example`](stack.env.example).
