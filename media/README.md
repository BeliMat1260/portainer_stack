# Stack Portainer: media

Stack multimediale che oggi su Portainer si chiama `stack`. Gluetun porta fuori
qBittorrent, Prowlarr e Trawl. Jellyfin, Seerr, Radarr, Sonarr e Tdarr restano
sulla rete Docker `media-network`.

Flaresolverr non c'è più: al suo posto c'è Trawl, sulla stessa porta `8191`.

## Porte sull'host

| Servizio | Porta |
| --- | --- |
| Gluetun control / proxy / Shadowsocks | 9075, 8888, 8388 |
| qBittorrent | `WEBUI_PORT_QBITTORRENT`, torrent `QBIT_PORT_TCP`/`UDP` |
| Prowlarr | `WEBUI_PORT_PROWLARR` |
| Trawl | `FLARESOLVERR_PORT` |
| Jellyfin, Seerr, Radarr, Sonarr, Tdarr | le `WEBUI_PORT_*` in `stack.env.example` |

Proxy HTTP e Shadowsocks di Gluetun sono aperti sulla LAN. Non inoltrarli dal
router verso internet.

## Dati che non vanno persi

Configurazioni e librerie sono bind mount, non volumi nominati:

- `${FOLDER_FOR_CONFIGS}/gluetun`, `jellyfin`, `seerr`, `prowlarr`, `qbittorrent`, `radarr`, `sonarr`, `tdarr`, `trawl-redis`
- media in `${FOLDER_FOR_MEDIA}`, download in `${FOLDER_FOR_DOWNLOADS}`
- Radarr e Sonarr vedono tutto `${FOLDER_FOR_HDD}` montato su `/hdd`, come prima

## Deploy

Compose path: `media/docker-compose.yml`.

Nome stack su Portainer: `media`. Prima rimuovi lo stack vecchio `stack` da
*Stacks* senza cancellare le cartelle sotto `/DATA/AppData/media-stack` e `/hdd`.
I `container_name` sono gli stessi: se i container vecchi esistono ancora, il
deploy si ferma.

Incolla `stack.env.example` nelle *Environment variables* e sostituisci
`VPN_USERNAME` e `VPN_PASSWORD`. Le variabili WireGuard vuote vanno lasciate
vuote finché `VPN_TYPE=openvpn`.

Questo stack non ha Watchtower. Un aggiornamento notturno di Jellyfin o dei
*arr* può rompere la libreria: si aggiorna da Portainer con *Update the stack*.
`pull_policy: always` riscarica le immagini in quel momento.

`restart` è `unless-stopped`: un container fermato da Portainer resta fermo.
