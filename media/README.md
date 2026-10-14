# Media

Libreria, richieste e download. Jellyfin riproduce, Seerr raccoglie le richieste, Radarr e Sonarr le passano a qBittorrent. Prowlarr cerca gli indexer. Il traffico di qBittorrent, Prowlarr e Trawl esce da Gluetun. Tdarr transcodifica.

| | |
| --- | --- |
| Progetto | `media-stack` |
| Compose | [docker-compose.yml](docker-compose.yml) |
| Variabili | [stack.env.example](stack.env.example) |
| Rete | `media-network` · `172.28.10.0/24` |

Le porte sono quelle di `stack.env.example`. [Installazione](../README.md#installazione).

## Dietro la VPN

Questi servizi condividono la rete di Gluetun, che pubblica le loro porte.

| Servizio | Porta sull'host |
| --- | --- |
| qBittorrent | web `8200`, torrent `6881` TCP/UDP |
| Prowlarr | `9696` |
| Trawl | `8191` |
| Redis di Trawl | `localhost`, dentro la rete di Gluetun |

Gluetun pubblica anche il control server `9075`, il proxy HTTP `8888` e Shadowsocks `8388`. Proxy e Shadowsocks sono raggiungibili dalla LAN (`LOCAL_SUBNET`).

## Sulla rete dello stack

| Servizio | Porta sull'host |
| --- | --- |
| Jellyfin | `8096` |
| Seerr | `5055` |
| Radarr | `7878` |
| Sonarr | `8989` |
| Tdarr | web `8265`, server `8266` |

Jellyfin, Seerr, Radarr e Sonarr usano i DNS `8.8.8.8` e `1.1.1.1`. Prowlarr, qBittorrent, Radarr e Sonarr usano il tema `TP_THEME` (`nord`).

## Dati

| Percorso | Contenuto |
| --- | --- |
| `/DATA/AppData/media-stack` | Configurazioni dei servizi |
| `/hdd/Media` | Libreria per Jellyfin e Tdarr |
| `/hdd/Downloads` | Download di qBittorrent |
| `/hdd` | Disco visto da Radarr e Sonarr |
| `/mnt/temp/tdarr_transcode_cache` | Cache di transcodifica |

`FOLDER_FOR_HDD` e `TDARR_TRANSCODE_PATH`, se assenti, valgono `/hdd` e `/mnt/temp/tdarr_transcode_cache`.

## VPN

Proton via OpenVPN: `VPN_SERVICE_PROVIDER=protonvpn`, `VPN_TYPE=openvpn`, credenziali in `VPN_USERNAME` e `VPN_PASSWORD`. Il paese nell'esempio è `Romania`. Con OpenVPN i campi WireGuard restano vuoti.
