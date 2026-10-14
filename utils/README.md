# Utils

Nextcloud, il tunnel Cloudflare, Syncthing, File Browser, Gitea e Crafty.

Nome su Portainer: `utils`. Il compose fissa `name: utils`, quindi i volumi di
File Browser sono `utils_filebrowser_database` e `utils_filebrowser_config`.

Compose path: `utils/docker-compose.yml`.
Rete Docker: `utils-network`, subnet `172.20.0.0/16`.

## Servizi

| Servizio | Porta sull'host | Dati |
| --- | --- | --- |
| Nextcloud | `WEBUI_PORT_NEXTCLOUD` (`8181`) | `${FOLDER_FOR_CONFIGS}/nextcloud` e `${FOLDER_FOR_MEDIA}/Nextcloud` |
| MariaDB | interna | `${FOLDER_FOR_CONFIGS}/mariadb`, database `nextcloud` |
| Cloudflared | rete dell'host | token in `CLOUDFLARED_TUNNEL_TOKEN` |
| Syncthing | web `8384`, sync `22000`, discovery `21027` | `${FOLDER_FOR_CONFIGS}/syncthing` e `${FOLDER_FOR_MEDIA}/Syncthing` |
| File Browser | `WEBUI_PORT_FILEBROWSER` (`8080`) | mostra `FILEBROWSER_ROOT` (esempio `/hdd`) |
| Gitea | web `1111`, SSH `222` | `${FOLDER_FOR_CONFIGS}/gitea`, dominio `GITEA_DOMAIN` |
| Crafty | web `8443`, Minecraft Java `25565`, Bedrock `19132/udp` | `${FOLDER_FOR_CONFIGS}/crafty` |

Cloudflared sta sulla rete dell'host e raggiunge le porte pubblicate dagli
altri container. Gitea riceve `GITEA_DOMAIN`, `GITEA_ROOT_URL` e
`GITEA_SSH_PORT`.

Le password di MariaDB sono `NEXTCLOUD_DB_PASSWORD` e
`NEXTCLOUD_DB_ROOT_PASSWORD`. Con un database già creato vanno usate le
password con cui è stato inizializzato.

Il modello delle variabili è [`stack.env.example`](stack.env.example).
L'aggiornamento delle immagini è *Update the stack* su Portainer.
