# Stack Portainer: n8n

n8n con Postgres 15. Compose path: `n8n/docker-compose.yml`. Il nome stack su
Portainer resta `n8n`: il compose fissa `name: n8n`, così i volumi già creati
(`n8n_n8n-postgres` e `n8n_n8n-data`) restano quelli giusti.

Redis c'era nel compose ma n8n non lo usava (niente coda). Non è stato
riportato. Il volume `n8n_n8n-redis` si può cancellare da *Volumes* dopo il
passaggio, è vuoto di workflow.

## Chiave di cifratura

`N8N_ENCRYPTION_KEY` va copiata dal compose attuale su Portainer, non generata
di nuovo. Con una chiave diversa n8n non decifra più le credenziali dei nodi.
Password del database e basic auth vanno copiate allo stesso modo se Postgres
è già stato inizializzato: `POSTGRES_PASSWORD` vale solo alla prima creazione
del volume.

Il basic auth e `N8N_SECURE_COOKIE=false` restano come oggi, per l'accesso in
HTTP sulla LAN. Il socket Docker è ancora montato: i workflow che lanciano
container ne hanno bisogno, e chi controlla n8n controlla anche il Docker
dell'host.

Gli script esterni restano in `${FOLDER_FOR_SCRIPTS}` montato su `/script`.

## Deploy

1. Copia chiave, password database e basic auth dallo stack attuale.
2. Rimuovi lo stack `n8n` senza cancellare i volumi `n8n_n8n-postgres` e
   `n8n_n8n-data`.
3. Deploy da git con lo stesso nome stack.

n8n parte solo dopo che Postgres risponde a `pg_isready`. Non c'è Watchtower.
