# Utils

File, git, sincronizzazione, Minecraft e il tunnel che pubblica i servizi.

| | |
| --- | --- |
| Progetto | `utils` |
| Compose | [docker-compose.yml](docker-compose.yml) |
| Variabili | [stack.env.example](stack.env.example) |
| Rete | `utils-network` · `172.20.0.0/16` |

`name: utils` tiene i volumi di File Browser su `utils_filebrowser_database` e `utils_filebrowser_config`. Le porte sono quelle di `stack.env.example`. [Installazione](../README.md#installazione).

## Servizi

| Servizio | Porta sull'host | Dati |
| --- | --- | --- |
| Nextcloud | `8181` | config in `/DATA/AppData/media-stack/nextcloud`, file in `/hdd/Nextcloud` |
| MariaDB | solo rete interna | `/DATA/AppData/media-stack/mariadb`, database `nextcloud` |
| Cloudflared | rete dell'host | token in `CLOUDFLARED_TUNNEL_TOKEN` |
| Syncthing | web `8384`, sync `22000`, discovery `21027` | config in `/DATA/AppData/media-stack/syncthing`, cartella `/hdd/Syncthing` |
| File Browser | `8080` | la cartella `FILEBROWSER_ROOT` |
| Gitea | web `1111`, SSH `222` | `/DATA/AppData/media-stack/gitea` |
| Crafty | web `8443`, Java `25565`, Bedrock `19132/udp` | `/DATA/AppData/media-stack/crafty` |

Cloudflared usa la rete dell'host per raggiungere le porte degli altri container. Gitea legge dominio e URL da `GITEA_DOMAIN`, `GITEA_ROOT_URL` e `GITEA_SSH_PORT` (nell'esempio, `git.example.com`). Le password di MariaDB sono quelle con cui il database è stato creato.

Se non sono nello stack, i DNS restano `8.8.8.8` e `1.1.1.1`, File Browser sta sulla porta `8080` e mostra `/`, Minecraft Java è `25565` e Bedrock `19132/udp`. Nell'esempio `FILEBROWSER_ROOT` è `/hdd`.
