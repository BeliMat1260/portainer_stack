# Utils

Il resto della casa, intorno ai media: i file di Nextcloud, il git, la cartella
sincronizzata, il pannello di Minecraft, il browser dei file e il tunnel
Cloudflare che pubblica i siti.

Su Portainer il nome resta `utils`. Il compose fissa `name: utils`, così i
volumi di File Browser (`utils_filebrowser_database` e
`utils_filebrowser_config`) restano quelli già creati.

Compose path: `utils/docker-compose.yml`.

## Chi c'è

| Servizio | Porta sull'host | A cosa serve |
| --- | --- | --- |
| Nextcloud + MariaDB | `WEBUI_PORT_NEXTCLOUD` (`8181`) | File e calendario. I dati stanno in `${FOLDER_FOR_MEDIA}/Nextcloud` |
| Cloudflared | rete dell'host | Pubblica i siti. Il token sta in `CLOUDFLARED_TUNNEL_TOKEN` |
| Syncthing | `8384`, sync `22000`, discovery `21027` | Cartelle tenute allineate, dati in `${FOLDER_FOR_MEDIA}/Syncthing` |
| File Browser | `WEBUI_PORT_FILEBROWSER` (`8080`) | Sfoglia `FILEBROWSER_ROOT` |
| Gitea | web `WEBUI_PORT_GITEA` (`1111`), SSH `GITEA_SSH_PORT` (`222`) | Git di casa, su `GITEA_DOMAIN` |
| Crafty | `WEBUI_PORT_CRAFTY` (`8443`), Minecraft `25565` e `19132/udp` | Server di Minecraft |

Cloudflared usa la rete dell'host per raggiungere le porte degli altri
container. Il resto dello stack sta sulla rete `utils-network`
(`172.20.0.0/16`), diversa da quella di `media`.

## Cosa vive altrove

Nel vecchio stack `utils` c'erano variabili di servizi che ora hanno una
cartella propria, o che non ci sono più:

- i giochi gratis sono in [free-games-claimer](../free-games-claimer/)
- il DNS è in [pihole](../pihole/)
- Project Zomboid non ha un compose nel backup. Le variabili `PZ_*` non sono state trasformate in un server vuoto

## Prima del deploy

1. Copia le password di MariaDB già in uso. Se il database in
   `${FOLDER_FOR_CONFIGS}/mariadb` esiste, una password nuova nel compose non
   entra: MySQL legge la password alla prima creazione del volume.
2. Crea un token nuovo del tunnel su Cloudflare e mettilo in
   `CLOUDFLARED_TUNNEL_TOKEN`. Quello vecchio era scritto nel compose.
3. Scegli `FILEBROWSER_ROOT`. Prima era `/`, quindi il container vedeva
   l'intero host, socket Docker compreso. L'esempio usa `/hdd`. Il login di
   File Browser resta nel volume nominato.
4. Rimuovi lo stack `utils` senza cancellare `/DATA/AppData/media-stack`.
5. Ricrealo da git con lo stesso nome.

`GITEA_DOMAIN` e `GITEA_ROOT_URL` arrivano a Gitea come
`GITEA__server__DOMAIN` e `GITEA__server__ROOT_URL`. Se `app.ini` ha già gli
stessi valori, il sito risponde come prima.

Nextcloud e Gitea si aggiornano con *Update the stack*, quando decidi tu.
