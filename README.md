# Homelab

Gli stack Docker di casa, pronti da distribuire con Portainer da questo
repository. Ogni cartella è una stanza a sé: si accende, si aggiorna e si
spegne senza toccare le altre.

Il repository è pubblico. Le password restano nelle *Environment variables*
di Portainer. `stack.env.example` contiene solo segnaposto.

## Le stanze

| Stack | Nome su Portainer | A cosa serve | Compose path |
| --- | --- | --- | --- |
| [media](media/) | `media` | Film, serie, richieste e download. Il traffico torrent esce dalla VPN | `media/docker-compose.yml` |
| [utils](utils/) | `utils` | Nextcloud, Git, file, Minecraft e il tunnel che pubblica i siti | `utils/docker-compose.yml` |
| [homepage](homepage/) | `homepage` | La bacheca che riassume il resto della casa | `homepage/docker-compose.yml` |
| [pihole](pihole/) | `pihole` | DNS della LAN, con la web UI sulla porta 8081 | `pihole/docker-compose.yml` |
| [n8n](n8n/) | `n8n` | Automazioni, con Postgres che tiene i workflow | `n8n/docker-compose.yml` |
| [pterodactyl](pterodactyl/) | `pterodactyl` | Pannello e Wings per i server di gioco | `pterodactyl/docker-compose.yml` |
| [free-games-claimer](free-games-claimer/) | `free_games` | Riscatta i giochi gratis e si aggiorna da solo | `free-games-claimer/docker-compose.yml` |

Il sito personale vive in
[BeliMat1260/Portfolio](https://github.com/BeliMat1260/Portfolio). Lo pubblica
il tunnel Cloudflare dello stack `utils`.

Project Zomboid non ha uno stack qui. Nel vecchio Portainer c'erano solo
variabili `PZ_*`, senza un servizio.

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

Le variabili arrivano al compose come `${VARIABILE}`. Il riquadro "stack.env
file operation" di Portainer serve solo ai compose che usano `env_file:`.

## Dal compose incollato a mano a git

I dati restano sull'host. Cambia solo il posto da cui Portainer legge il file.

1. Copia le *Environment variables* dello stack vecchio. Le password non sono in git.
2. Per n8n copia `N8N_ENCRYPTION_KEY` dal compose attuale. Una chiave diversa
   rende illeggibili le credenziali dei workflow.
3. Per Pi-hole copia `/data/compose/<id>/etc-pihole` in `/DATA/AppData/pihole`
   prima di rimuovere lo stack.
4. Crea un token nuovo del tunnel Cloudflare e mettilo in
   `CLOUDFLARED_TUNNEL_TOKEN`. Quello precedente era scritto nel compose.
5. Rimuovi lo stack vecchio. Lascia intatte le cartelle in `/DATA/AppData` e i
   volumi `n8n_n8n-postgres`, `n8n_n8n-data`, `utils_filebrowser_*`,
   `pterodactyl_database_data`.
6. Ricrea lo stack da questo repository, con il nome della tabella.

`media` usa la subnet `172.28.10.0/24`, `utils` usa `172.20.0.0/16`. Vanno
tenute diverse tra loro e diverse da `docker0`.

## Aggiornamenti

`pull_policy: always` riscarica l'immagine quando su Portainer fai *Update the
stack*. Gli stack di casa si aggiornano così, a mano: un update notturno di
Jellyfin, Nextcloud o n8n può rompere i dati.

L'eccezione è [free-games-claimer](free-games-claimer/): il suo Watchtower
controlla solo i container con scope `free-games-claimer`.

Con *GitOps updates* → *Polling*, Portainer ridistribuisce lo stack quando il
compose cambia su `main`. *Force redeployment* spento, altrimenti i container
ripartono a ogni controllo.

## Aggiungere una stanza

1. Nuova cartella, con `docker-compose.yml`, `stack.env.example` e `README.md`.
2. Nel compose: `pull_policy: always`, `restart: unless-stopped`, variabili
   obbligatorie scritte `${NOME:?err}`, dati in `${FOLDER_FOR_CONFIGS}/<nome>`.
3. Se serve l'aggiornamento automatico, un Watchtower con scope uguale al nome
   dello stack, come in [free-games-claimer](free-games-claimer/docker-compose.yml).
4. Una riga nella tabella qui sopra.
5. Prima del commit:

   ```bash
   docker compose --env-file <nome>/stack.env.example -f <nome>/docker-compose.yml config
   ```

## Quando Portainer si lamenta

| Errore | Cosa è successo | Cosa fare |
| --- | --- | --- |
| `additional properties '<nome>' not allowed` | Un servizio è finito fuori da `services:` | Usa il compose del repository |
| `open /data/compose/<n>/... no such file` | *Compose path* sbagliato | Percorso relativo alla root, per esempio `media/docker-compose.yml` |
| `required variable <NOME> is missing a value` | Manca una variabile | L'elenco è in `stack.env.example` |
| `container name "/<nome>" is already in use` | Il container del vecchio stack è ancora lì | Eliminalo da *Containers*. Le cartelle dati restano |
| `Pool overlaps with other one` | Due reti sulla stessa subnet | `media` è `172.28.10.0/24`, `utils` è `172.20.0.0/16` |
