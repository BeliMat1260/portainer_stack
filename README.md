# Homelab

Gli stack Docker di casa, da distribuire con Portainer da questo repository.
Ogni cartella è uno stack indipendente.

Il repository è pubblico. Le password stanno nelle *Environment variables* di
Portainer. `stack.env.example` contiene solo segnaposto.

## Stack

| Stack | Nome su Portainer | Contenuto | Compose path |
| --- | --- | --- | --- |
| [media](media/) | `media` | Jellyfin, Seerr, Radarr, Sonarr, Tdarr, qBittorrent, Prowlarr, Gluetun, Trawl | `media/docker-compose.yml` |
| [utils](utils/) | `utils` | Nextcloud, Cloudflare Tunnel, Syncthing, File Browser, Gitea, Crafty | `utils/docker-compose.yml` |
| [homepage](homepage/) | `homepage` | Dashboard Homepage | `homepage/docker-compose.yml` |
| [pihole](pihole/) | `pihole` | DNS Pi-hole | `pihole/docker-compose.yml` |
| [n8n](n8n/) | `n8n` | n8n e Postgres | `n8n/docker-compose.yml` |
| [pterodactyl](pterodactyl/) | `pterodactyl` | Pannello Pterodactyl e Wings | `pterodactyl/docker-compose.yml` |
| [free-games-claimer](free-games-claimer/) | `free_games` | Riscatto dei giochi gratis, con Watchtower | `free-games-claimer/docker-compose.yml` |

## Accendere uno stack

1. *Stacks* → *Add stack* → **Repository**. Il nome è quello della tabella.
2. Git:

   | Campo | Valore |
   | --- | --- |
   | Authentication | spenta, il repository è pubblico |
   | Repository URL | `https://github.com/BeliMat1260/portainer_stack` |
   | Repository reference | `refs/heads/main` |
   | Compose path | il percorso della tabella |

3. *Environment variables* → *Advanced mode*: incolla `stack.env.example` e
   sostituisci i segnaposto. Senza virgolette intorno ai valori.
4. *Deploy the stack*.

Le variabili arrivano al compose come `${VARIABILE}`.

## Aggiornamenti

`pull_policy: always` riscarica l'immagine quando su Portainer fai *Update the
stack*. L'unico Watchtower è in [free-games-claimer](free-games-claimer/) e
aggiorna solo i container con scope `free-games-claimer`.

Con *GitOps updates* → *Polling*, Portainer ridistribuisce lo stack quando il
compose cambia su `main`. *Force redeployment* spento, altrimenti i container
ripartono a ogni controllo.

`media` usa la subnet `172.28.10.0/24`, `utils` usa `172.20.0.0/16`.

## Aggiungere uno stack

1. Nuova cartella, con `docker-compose.yml`, `stack.env.example` e `README.md`.
2. Nel compose: `pull_policy: always`, `restart: unless-stopped`, variabili
   obbligatorie scritte `${NOME:?err}`, dati in `${FOLDER_FOR_CONFIGS}/<nome>`.
3. Una riga nella tabella qui sopra.
4. Prima del commit:

   ```bash
   docker compose --env-file <nome>/stack.env.example -f <nome>/docker-compose.yml config
   ```

## Errori di Portainer

| Errore | Cosa fare |
| --- | --- |
| `additional properties '<nome>' not allowed` | Usa il compose del repository |
| `open /data/compose/<n>/... no such file` | *Compose path* relativo alla root, per esempio `media/docker-compose.yml` |
| `required variable <NOME> is missing a value` | Aggiungi la variabile elencata in `stack.env.example` |
| `container name "/<nome>" is already in use` | Elimina il container esistente da *Containers* |
| `Pool overlaps with other one` | `media` è `172.28.10.0/24`, `utils` è `172.20.0.0/16` |
