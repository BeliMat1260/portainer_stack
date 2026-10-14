# Pterodactyl

Pannello per i server di gioco, con MariaDB 10.5, Redis e Wings. Wings crea i server tramite il socket Docker dell'host.

| | |
| --- | --- |
| Progetto | `pterodactyl` |
| Compose | [docker-compose.yml](docker-compose.yml) |
| Variabili | [stack.env.example](stack.env.example) |
| Database | MariaDB, utente `pterodactyl`, database `panel` |

Il volume del database è `pterodactyl_database_data`. Le porte sono quelle di `stack.env.example`. [Installazione](../README.md#installazione).

## Porte

| Servizio | Porta sull'host |
| --- | --- |
| Pannello | `5080` |
| API di Wings | `8480` |
| SFTP di Wings | `2022` |

## Dati

| Percorso | Contenuto |
| --- | --- |
| `/DATA/AppData/pterodactyl/panel` | File del pannello |
| `/DATA/AppData/pterodactyl/wings` | Configurazione di Wings |
| `/var/lib/pterodactyl` | Server di gioco, sull'host |
| `pterodactyl_database_data` | Dati di MariaDB |

L'indirizzo del pannello è `PTERO_APP_URL`. Wings gira con uid e gid `988`. `PTERO_DB_PASSWORD` e `PTERO_DB_ROOT_PASSWORD` sono le password con cui MariaDB è stato creato.
