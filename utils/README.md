# Stack Portainer: utils

Nextcloud, tunnel Cloudflare, Syncthing, File Browser, Gitea e Crafty.
Su Portainer lo stack si chiama già `utils`: tieni questo nome. I volumi di
File Browser si chiamano `utils_filebrowser_database` e `utils_filebrowser_config`
perché il compose fissa `name: utils`.

Compose path: `utils/docker-compose.yml`.

## Cosa è uscito da questo stack

Nel backup queste cose erano variabili dello stack `utils`, ma non servizi di
questo compose:

- free-games-claimer è in [free-games-claimer](../free-games-claimer/)
- Pi-hole è in [pihole](../pihole/)
- Project Zomboid non ha un compose nel backup (restano solo le vecchie
  variabili `PZ_*`). Non è stato ricreato un server di gioco vuoto

## Segreti

`CLOUDFLARED_TUNNEL_TOKEN`, `NEXTCLOUD_DB_PASSWORD` e
`NEXTCLOUD_DB_ROOT_PASSWORD` stanno solo nelle *Environment variables*.
Il token del tunnel era in chiaro nel compose: creane uno nuovo su Cloudflare
e non riusare quello del backup.

Se MariaDB è già stato inizializzato in `${FOLDER_FOR_CONFIGS}/mariadb`, le
password devono essere quelle di allora. Cambiarle nel compose non le aggiorna
dentro il database.

## File Browser

`FILEBROWSER_ROOT` è la cartella montata su `/srv`. Il valore precedente era
`/`, quindi il container vedeva l'intero host, socket Docker compreso. L'esempio
usa `/hdd`. Il database di File Browser resta nel volume nominato: il primo
login già fatto sopravvive al nuovo deploy.

## Gitea

`GITEA_DOMAIN` e `GITEA_ROOT_URL` c'erano su Portainer ma il compose non le
leggeva. Ora arrivano al container come `GITEA__server__DOMAIN` e
`GITEA__server__ROOT_URL`. Se `app.ini` ha già gli stessi valori, il
comportamento non cambia.

## Deploy

1. Copia da Portainer le password del database Nextcloud già in uso.
2. Ruota il token Cloudflare e metti quello nuovo in `CLOUDFLARED_TUNNEL_TOKEN`.
3. Rimuovi lo stack `utils` senza cancellare `/DATA/AppData/media-stack`.
4. Ricrealo da git con compose path `utils/docker-compose.yml` e lo stesso nome `utils`.

Cloudflared usa la rete dell'host, come prima, così raggiunge le porte pubblicate
dagli altri container. Non ha Watchtower: Nextcloud e Gitea si aggiornano solo
quando lanci *Update the stack*.
