# Pterodactyl

I server di gioco. Il pannello è la reception, Wings è chi accende davvero le
istanze parlando con Docker sull'host. In mezzo ci sono MariaDB 10.5 e Redis.

Compose path: `pterodactyl/docker-compose.yml`. Nome stack: `pterodactyl`,
così il volume `pterodactyl_database_data` resta quello già creato.

## Le porte

| Pezzo | Porta sull'host | Dentro al container |
| --- | --- | --- |
| Pannello | `WEBUI_PORT_PTERODACTYL` (`5080`) | `80` |
| API di Wings | `WINGS_API_PORT` (`8480`) | `8080` |
| SFTP di Wings | `WINGS_SFTP_PORT` (`2022`) | `2022` |

`PTERO_APP_URL` è l'indirizzo con cui apri il pannello, porta inclusa, per
esempio `http://192.168.1.90:5080`. Wings monta il socket Docker: è un servizio
da amministrazione. Davanti a internet ci vuole un proxy, non la porta nuda.

## Il database già esistente

`PTERO_DB_PASSWORD` e `PTERO_DB_ROOT_PASSWORD` devono essere le password della
prima creazione del volume. Nel compose precedente i segnaposto erano
`password_segreta_db` e `password_segreta_root`: se il database è nato con
quelli, vanno rimessi identici. Una password nuova non apre un MySQL già
inizializzato.

I file del pannello stanno in `${FOLDER_FOR_CONFIGS}/pterodactyl/panel`, la
configurazione di Wings in `${FOLDER_FOR_CONFIGS}/pterodactyl/wings`. I volumi
nominati `panel_data` e `wings_config` del compose vecchio non erano montati
da nessuno e non sono stati ridichiarati.

`restart: unless-stopped` lascia fermo un container che hai spento da
Portainer. L'immagine `:latest` del pannello si aggiorna con *Update the stack*.
