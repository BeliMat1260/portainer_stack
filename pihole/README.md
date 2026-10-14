# Pi-hole

Il DNS di casa. I dispositivi della LAN chiedono prima qui, e Pi-hole decide
cosa rispondere. Gira sulla rete dell'host, così può prendere la porta `53`.
In quella modalità le porte non si mappano: la web UI sta direttamente su
`WEBUI_PORT_PIHOLE`, oggi `8081`.

Compose path: `pihole/docker-compose.yml`. Nome stack nuovo: `pihole`.
Quello vecchio si chiama `pihole_stack`.

## Tenere le liste e la password

| Cosa | Variabile | Note |
| --- | --- | --- |
| Password della web UI | `PIHOLE_PASSWORD` | Diventa `FTLCONF_webserver_api_password` |
| DNS a monte | `PIHOLE_UPSTREAMS` | Oggi `1.1.1.1;9.9.9.9` |
| Fuso | `TIMEZONE` | `Europe/Rome` |
| Dati | `${FOLDER_FOR_CONFIGS}/pihole` | Liste, query, configurazione |

La password stava scritta nel compose. Su Portainer metti quella attuale, poi
cambiala dalla web UI.

## Spostare i dati prima di spegnere il vecchio stack

Il compose precedente montava `./etc-pihole`, cioè
`/data/compose/<id>/etc-pihole` sull'host di Portainer. Copia quella cartella
in `/DATA/AppData/pihole` e solo dopo rimuovi `pihole_stack`.

Il container si chiama ancora `pihole`. Due stack non possono tenerlo insieme:
togli il vecchio, poi fai il deploy da git.
