# n8n

Automazioni. I workflow stanno su Postgres 15 e n8n parte quando il database risponde. Il socket Docker è montato, quindi un workflow può avviare container.

| | |
| --- | --- |
| Progetto | `n8n` |
| Compose | [docker-compose.yml](docker-compose.yml) |
| Variabili | [stack.env.example](stack.env.example) |
| Porta sull'host | `5678` |

`name: n8n` fissa i volumi `n8n_n8n-postgres` e `n8n_n8n-data`. [Installazione](../README.md#installazione).

## Dati e accesso

| | |
| --- | --- |
| Database | Postgres 15, utente e nome `n8n` |
| Accesso | basic auth. `N8N_SECURE_COOKIE=false` per l'HTTP in LAN |
| Script | `/DATA/AppData/n8n`, montato su `/script` |
| Cifratura | `N8N_ENCRYPTION_KEY`, la stessa per tutta la vita dei workflow |
| Password Postgres | `N8N_DB_PASSWORD`, quella della creazione del volume |
