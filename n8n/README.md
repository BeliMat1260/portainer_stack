# n8n

Le automazioni. I workflow stanno su Postgres 15, n8n li esegue e, se un nodo
deve parlare con Docker, ha il socket dell'host a disposizione. Chi entra in
n8n può quindi anche gestire i container: tienilo in LAN, dietro il basic auth.

Compose path: `n8n/docker-compose.yml`. Il nome su Portainer resta `n8n`.
Il compose fissa `name: n8n`, così i volumi già pieni restano agganciati:
`n8n_n8n-postgres` e `n8n_n8n-data`.

## La chiave, prima di tutto

`N8N_ENCRYPTION_KEY` va copiata dal compose che gira adesso. Con una chiave
nuova n8n non legge più le credenziali salvate nei nodi.

Stesso discorso per `N8N_DB_PASSWORD` e per la password del basic auth, se il
volume di Postgres esiste già: `POSTGRES_PASSWORD` viene letta solo alla prima
creazione del database.

## Cosa parte

| Servizio | Dettaglio |
| --- | --- |
| Postgres 15 | Deve rispondere a `pg_isready` prima che n8n si avvii |
| n8n | Porta `WEBUI_PORT_N8N` (`5678`). Fuso `TIMEZONE`, cookie sicuri disattivati con `N8N_SECURE_COOKIE=false` perché l'accesso è in HTTP |
| Script | `${FOLDER_FOR_SCRIPTS}` montato su `/script`, oggi `/DATA/AppData/n8n` |

Redis c'era nel compose vecchio e n8n non lo usava. Il volume `n8n_n8n-redis`
si può cancellare da *Volumes* dopo il passaggio: i workflow sono nell'altro volume.

## Passaggio

1. Copia chiave, password del database e basic auth.
2. Rimuovi lo stack `n8n` lasciando `n8n_n8n-postgres` e `n8n_n8n-data`.
3. Deploy da git, stesso nome.

Si aggiorna con *Update the stack*, quando lo decidi tu.
