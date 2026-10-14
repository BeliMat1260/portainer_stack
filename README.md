# portainer_stack

Homelab in Docker Compose, uno stack per cartella, da installare con Portainer a partire da questo repository.

[Stack](#stack) · [Installazione](#installazione) · [Aggiornamenti](#aggiornamenti) · [Reti](#reti)

> Repository pubblico. Ogni `stack.env.example` contiene segnaposto. Password, token e chiavi si incollano nelle *Environment variables* di Portainer.

## Stack

| Cartella | Progetto | Compose path | Contenuto |
| --- | --- | --- | --- |
| [media](media/) | `media-stack` | `media/docker-compose.yml` | Jellyfin, Seerr, Radarr, Sonarr, Prowlarr, qBittorrent, Gluetun, Trawl, Tdarr |
| [utils](utils/) | `utils` | `utils/docker-compose.yml` | Nextcloud, Cloudflare Tunnel, Syncthing, File Browser, Gitea, Crafty |
| [homepage](homepage/) | `homepage` | `homepage/docker-compose.yml` | Dashboard dei servizi |
| [pihole](pihole/) | `pihole` | `pihole/docker-compose.yml` | DNS della rete locale |
| [n8n](n8n/) | `n8n` | `n8n/docker-compose.yml` | Automazioni, con Postgres |
| [pterodactyl](pterodactyl/) | `pterodactyl` | `pterodactyl/docker-compose.yml` | Pannello di gioco e Wings |
| [free-games-claimer](free-games-claimer/) | `free_games` | `free-games-claimer/docker-compose.yml` | Riscatto dei giochi gratis |

Il progetto è il valore di `name:` nel compose: è quello che dà il nome a reti e volumi. Su Portainer lo stack può chiamarsi in un altro modo. [free-games-claimer](free-games-claimer/) è l'eccezione, perché il compose non fissa `name:` e lo stack va creato come `free_games`.

## Installazione

1. In Portainer: *Stacks* → *Add stack* → **Repository**.
2. URL `https://github.com/BeliMat1260/portainer_stack`, reference `refs/heads/main`. L'autenticazione del repository resta disattivata.
3. *Compose path*: la colonna della tabella. Per utils è `utils/docker-compose.yml`.
4. *Environment variables* → *Advanced mode*: incolla il `stack.env.example` della stessa cartella e sostituisci i segnaposto, senza virgolette.

Il compose legge `${VARIABILE}`. Se manca un valore obbligatorio il deploy si ferma con `required variable … is missing a value`.

## Aggiornamenti

*Update the stack* riscarica le immagini (`pull_policy: always`). L'unico aggiornamento automatico è il Watchtower di [free-games-claimer](free-games-claimer/), limitato ai container di quello stack.

Con *GitOps updates* → *Polling*, Portainer segue `main`. *Force redeployment* resta spento.

## Reti

| Progetto | Subnet |
| --- | --- |
| `media-stack` | `172.28.10.0/24` |
| `utils` | `172.20.0.0/16` |

## In ogni cartella

```text
<stack>/
├── docker-compose.yml
├── stack.env.example
└── README.md
```

Per aggiungerne uno: stessa struttura, variabili obbligatorie nella forma `${NOME:?err}`, una riga nella tabella, poi

```bash
docker compose --env-file <stack>/stack.env.example -f <stack>/docker-compose.yml config
```
