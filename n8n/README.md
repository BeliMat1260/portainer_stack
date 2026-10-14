# n8n

Automazioni, con Postgres 15 che conserva i workflow. n8n ha il socket Docker
dell'host, quindi un workflow può avviare container.

Nome su Portainer: `n8n`. Il compose fissa `name: n8n`: i volumi sono
`n8n_n8n-postgres` e `n8n_n8n-data`.

Compose path: `n8n/docker-compose.yml`.

| Servizio | Dettaglio |
| --- | --- |
| Postgres 15 | n8n parte dopo che `pg_isready` risponde |
| n8n | Porta `WEBUI_PORT_N8N` (`5678`), fuso `TIMEZONE`, basic auth attivo, `N8N_SECURE_COOKIE=false` |
| Script | `${FOLDER_FOR_SCRIPTS}` (`/DATA/AppData/n8n`) montato su `/script` |

`N8N_ENCRYPTION_KEY` è la chiave con cui n8n cifra le credenziali dei nodi.
Su un'installazione già in uso va riusata la chiave esistente.
`N8N_DB_PASSWORD` è la password di Postgres, anch'essa quella della prima
creazione del volume.

Il modello è [`stack.env.example`](stack.env.example).
L'aggiornamento delle immagini è *Update the stack* su Portainer.
