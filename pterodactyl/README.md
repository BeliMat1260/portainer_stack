# Pterodactyl

Pannello per i server di gioco, con MariaDB 10.5, Redis e Wings. Wings usa il
socket Docker dell'host per creare i server.

Nome su Portainer: `pterodactyl`. Il volume del database è
`pterodactyl_database_data`.

Compose path: `pterodactyl/docker-compose.yml`.

| Servizio | Porta sull'host |
| --- | --- |
| Pannello | `WEBUI_PORT_PTERODACTYL` (`5080`) |
| API di Wings | `WINGS_API_PORT` (`8480`) |
| SFTP di Wings | `WINGS_SFTP_PORT` (`2022`) |

`PTERO_APP_URL` è l'indirizzo del pannello, porta inclusa.
I file del pannello sono in `${FOLDER_FOR_CONFIGS}/pterodactyl/panel`, la
configurazione di Wings in `${FOLDER_FOR_CONFIGS}/pterodactyl/wings`.

`PTERO_DB_PASSWORD` e `PTERO_DB_ROOT_PASSWORD` sono le password di MariaDB.
Su un database già creato vanno riusate quelle della prima inizializzazione.

Il modello è [`stack.env.example`](stack.env.example).
L'aggiornamento delle immagini è *Update the stack* su Portainer.
