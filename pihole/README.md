# Stack Portainer: pihole

Pi-hole in `network_mode: host`. La DNS è la porta 53 dell'host; la web UI è
`WEBUI_PORT_PIHOLE` (oggi 8081). Compose path: `pihole/docker-compose.yml`.

La password non è più nel compose. Su Portainer si chiama `PIHOLE_PASSWORD`
(`FTLCONF_webserver_api_password`). Se Pi-hole è già in uso, metti la password
attuale, poi cambiala dall'interfaccia: quella nel vecchio compose era in chiaro.

I dati persistenti sono in `${FOLDER_FOR_CONFIGS}/pihole`. Prima il compose
montava `./etc-pihole`, cioè una cartella relativa alla directory dello stack
su Portainer (`/data/compose/<id>/etc-pihole`). Prima del nuovo deploy copia
quella cartella in `/DATA/AppData/pihole`, oppure punta `FOLDER_FOR_CONFIGS`
al genitore della cartella già esistente e rinominala `pihole`.

Lo stack vecchio si chiama `pihole_stack`. Rimuovilo prima del deploy: il
container si chiama ancora `pihole` e due stack non possono tenerlo insieme.
Non cancellare la cartella `etc-pihole` finché non l'hai copiata.
