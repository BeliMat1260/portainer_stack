# Media

Il salotto. Jellyfin fa partire il film, Seerr raccoglie le richieste, Radarr e
Sonarr le trasformano in download, qBittorrent e Prowlarr escono da Gluetun.
Tdarr sistema i file. Trawl apre le pagine che prima passavano da Flaresolverr,
sulla stessa porta `8191`.

Su Portainer lo stack nuovo si chiama `media`. Quello vecchio si chiama `stack`.

Compose path: `media/docker-compose.yml`.

## Chi c'è

| Servizio | Dove vive | Note |
| --- | --- | --- |
| Gluetun | rete `media-network`, porte sull'host | VPN Proton, proxy HTTP `8888`, Shadowsocks `8388`, control `9075` |
| qBittorrent | dentro la rete di Gluetun | Web UI su `WEBUI_PORT_QBITTORRENT`, torrent su `QBIT_PORT_TCP` / `UDP` |
| Prowlarr | dentro la rete di Gluetun | Web UI su `WEBUI_PORT_PROWLARR` |
| Trawl + Redis | dentro la rete di Gluetun | API su `FLARESOLVERR_PORT`. Redis è `localhost` perché condividono la rete di Gluetun |
| Jellyfin | `media-network` | Libreria in `${FOLDER_FOR_MEDIA}` |
| Seerr | `media-network` | Richieste su `WEBUI_PORT_SEERR` |
| Radarr, Sonarr | `media-network` | Vedono tutto `${FOLDER_FOR_HDD}` montato su `/hdd` |
| Tdarr | `media-network` | Transcode in `${TDARR_TRANSCODE_PATH}` |

Le porte di default sono in [`stack.env.example`](stack.env.example): Jellyfin
`8096`, Seerr `5055`, Radarr `7878`, Sonarr `8989`, qBittorrent `8200`,
Prowlarr `9696`, Tdarr `8265`/`8266`.

Proxy e Shadowsocks di Gluetun sono aperti sulla LAN. Vanno bene in casa, non
sul port forwarding del router.

## I dischi

Tutto è un bind mount. Cancellare lo stack lascia i file dov'erano.

- configurazioni in `${FOLDER_FOR_CONFIGS}` (`/DATA/AppData/media-stack`): `gluetun`, `jellyfin`, `seerr`, `prowlarr`, `qbittorrent`, `radarr`, `sonarr`, `tdarr`, `trawl-redis`
- film e serie in `${FOLDER_FOR_MEDIA}` (`/hdd/Media`)
- download in `${FOLDER_FOR_DOWNLOADS}` (`/hdd/Downloads`)

## Accenderlo

1. Incolla [`stack.env.example`](stack.env.example) nelle *Environment variables*.
2. Metti utente e password della VPN in `VPN_USERNAME` e `VPN_PASSWORD`.
3. Con `VPN_TYPE=openvpn` lascia vuote le variabili WireGuard.
4. Rimuovi lo stack `stack` senza cancellare `/DATA/AppData/media-stack` e `/hdd`.
5. Deploy con nome `media`.

I container hanno gli stessi nomi di prima. Se quelli vecchi sono ancora accesi,
Portainer si ferma: eliminali da *Containers*, i file restano.

Questo stack si aggiorna a mano, con *Update the stack*. Un Watchtower notturno
su Jellyfin o sui *arr* può rompere la libreria. `restart: unless-stopped`
significa che un container fermato da Portainer resta fermo.

Jellyfin, Seerr, Radarr e Sonarr usano `DNS_PRIMARY` e `DNS_SECONDARY`
(`8.8.8.8` e `1.1.1.1`). Così, se Pi-hole è spento, il salotto resta raggiungibile.
