# Media

Film, serie, richieste e download. Jellyfin riproduce, Seerr raccoglie le
richieste, Radarr e Sonarr le mandano a qBittorrent. Prowlarr cerca gli
indexer. qBittorrent, Prowlarr e Trawl escono da Gluetun. Tdarr transcodifica.

Nome su Portainer: `media`.
Compose path: `media/docker-compose.yml`.

## Servizi

| Servizio | Rete | Porta sull'host |
| --- | --- | --- |
| Gluetun | `media-network` (`172.28.10.0/24`) | control `9075`, proxy HTTP `8888`, Shadowsocks `8388` |
| qBittorrent | rete di Gluetun | `WEBUI_PORT_QBITTORRENT` (`8200`), torrent `6881` TCP/UDP |
| Prowlarr | rete di Gluetun | `WEBUI_PORT_PROWLARR` (`9696`) |
| Trawl e Redis | rete di Gluetun | `FLARESOLVERR_PORT` (`8191`). Redis è su `localhost` |
| Jellyfin | `media-network` | `WEBUI_PORT_JELLYFIN` (`8096`) |
| Seerr | `media-network` | `WEBUI_PORT_SEERR` (`5055`) |
| Radarr | `media-network` | `WEBUI_PORT_RADARR` (`7878`) |
| Sonarr | `media-network` | `WEBUI_PORT_SONARR` (`8989`) |
| Tdarr | `media-network` | web `8265`, server `8266` |

Proxy e Shadowsocks di Gluetun sono aperti sulla LAN.

Jellyfin, Seerr, Radarr e Sonarr usano `DNS_PRIMARY` e `DNS_SECONDARY`
(`8.8.8.8` e `1.1.1.1`).

## Dati

| Percorso sull'host | Contenuto |
| --- | --- |
| `${FOLDER_FOR_CONFIGS}` (`/DATA/AppData/media-stack`) | Config di Gluetun, Jellyfin, Seerr, Prowlarr, qBittorrent, Radarr, Sonarr, Tdarr, Redis di Trawl |
| `${FOLDER_FOR_MEDIA}` (`/hdd/Media`) | Libreria, montata in Jellyfin e Tdarr |
| `${FOLDER_FOR_DOWNLOADS}` (`/hdd/Downloads`) | Download di qBittorrent |
| `${FOLDER_FOR_HDD}` (`/hdd`) | Disco intero visto da Radarr e Sonarr su `/hdd` |
| `${TDARR_TRANSCODE_PATH}` | Cache di transcodifica |

## Variabili

Il modello è [`stack.env.example`](stack.env.example). Obbligatorie per la VPN:
`VPN_TYPE`, `VPN_SERVICE_PROVIDER`, `VPN_USERNAME`, `VPN_PASSWORD`. Con
`VPN_TYPE=openvpn` le variabili WireGuard restano vuote.

L'aggiornamento delle immagini è *Update the stack* su Portainer.
