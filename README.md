# portainer_stack

Raccolta di stack Docker Compose per l'home server, pensati per essere distribuiti con
**Portainer** direttamente da questo repository Git (*Stacks* → *Add stack* → *Repository*).

Ogni cartella è uno stack indipendente: si distribuisce, si aggiorna e si rimuove senza
toccare gli altri.

## Stack disponibili

| Stack | Nome su Portainer | Descrizione | Compose path |
| --- | --- | --- | --- |
| [media](media/) | `media` (prima `stack`) | Jellyfin, Seerr, Radarr, Sonarr, Tdarr, qBittorrent e Prowlarr dietro Gluetun, Trawl al posto di Flaresolverr | `media/docker-compose.yml` |
| [utils](utils/) | `utils` | Nextcloud, Cloudflare Tunnel, Syncthing, File Browser, Gitea, Crafty | `utils/docker-compose.yml` |
| [homepage](homepage/) | `homepage` | Dashboard Homepage | `homepage/docker-compose.yml` |
| [pihole](pihole/) | `pihole` (prima `pihole_stack`) | DNS Pi-hole sulla rete dell'host | `pihole/docker-compose.yml` |
| [n8n](n8n/) | `n8n` | n8n con Postgres | `n8n/docker-compose.yml` |
| [pterodactyl](pterodactyl/) | `pterodactyl` | Pannello Pterodactyl e Wings | `pterodactyl/docker-compose.yml` |
| [free-games-claimer](free-games-claimer/) | `free_games` | Riscatto giochi gratis, con Watchtower limitato a questo stack | `free-games-claimer/docker-compose.yml` |

Il sito portfolio resta nel repository
[BeliMat1260/Portfolio](https://github.com/BeliMat1260/Portfolio): il compose path lì è
`docker-compose.yml`. Il tunnel che pubblica `mattiabelli.com` è lo stack `utils`.

Project Zomboid non è in questo repository. Nel backup c'erano solo le variabili
`PZ_*` dentro `utils`, senza un servizio.

## Struttura del repository

```
portainer_stack/
├── .gitignore                 # esclude ogni file *.env dai commit
├── README.md                  # questo file
└── <nome-stack>/
    ├── docker-compose.yml     # definizione dello stack
    ├── stack.env.example      # variabili da impostare su Portainer (solo segnaposto)
    └── README.md              # documentazione dello stack
```

## Distribuire uno stack con Portainer

1. *Stacks* → *Add stack*, dai un nome allo stack e scegli **Repository**.
2. Compila la sezione *Git repository*:

   | Campo | Valore |
   | --- | --- |
   | Authentication | spenta (il repository è pubblico) |
   | Repository URL | `https://github.com/BeliMat1260/portainer_stack` |
   | Repository reference | `refs/heads/main` |
   | Compose path | `<nome-stack>/docker-compose.yml` (relativo alla root del repository) |

3. *Environment variables* → *Advanced mode*: incolla il contenuto di
   `<nome-stack>/stack.env.example` e sostituisci i segnaposto con i valori veri.
   Niente virgolette intorno ai valori.
4. *Deploy the stack*.

Il riquadro di Portainer "stack.env file operation" riguarda solo i compose che usano
`env_file: stack.env`. Gli stack di questo repository leggono le variabili con la sintassi
`${VARIABILE}`, quindi basta inserirle nella sezione *Environment variables*.

## Aggiornamenti

- **Immagini:** gli stack che lo prevedono includono un'istanza di
  [Watchtower](https://github.com/nicholas-fedor/watchtower) limitata al solo stack (label
  `com.centurylinklabs.watchtower.scope`), che scarica le nuove versioni e ricrea i container
  mantenendo volumi, porte e variabili. Tutti i servizi hanno anche `pull_policy: always`,
  quindi *Update the stack* su Portainer riscarica sempre l'immagine.
- **File del repository:** attivando *GitOps updates* → *Polling* sullo stack, Portainer
  ridistribuisce lo stack quando cambia il `docker-compose.yml` su `main`. Lasciare spento
  *Force redeployment*, altrimenti i container vengono riavviati a ogni controllo.

## Aggiungere un nuovo stack

1. Crea una cartella con il nome dello stack.
2. Aggiungi `docker-compose.yml` con:
   - `pull_policy: always` e `restart: unless-stopped` su ogni servizio;
   - variabili obbligatorie nella forma `${NOME:?err}`, così il deploy si ferma subito
     con un messaggio chiaro se ne manca una;
   - dati persistenti montati in `${FOLDER_FOR_CONFIGS}/<nome-stack>`;
   - se serve l'aggiornamento automatico, un servizio Watchtower con `WATCHTOWER_SCOPE`
     uguale al nome dello stack e le label `watchtower.enable` / `watchtower.scope` sui
     container da aggiornare (vedi [free-games-claimer](free-games-claimer/docker-compose.yml)).
3. Aggiungi `stack.env.example` con **solo segnaposto**, mai credenziali vere.
4. Aggiungi un `README.md` e una riga nella tabella *Stack disponibili* qui sopra.
5. Verifica il file prima del commit:

   ```bash
   docker compose --env-file <nome-stack>/stack.env.example -f <nome-stack>/docker-compose.yml config
   ```

## Passare dagli stack incollati su Portainer a questo repository

Gli stack `media`, `utils`, `homepage`, `pihole`, `n8n` e `pterodactyl` sono la
versione da git di quelli che oggi vivono solo su Portainer. I dati restano
sull'host; cambia solo da dove Portainer legge il compose.

1. Apri lo stack vecchio e copia le *Environment variables*. Le password non
   sono in git.
2. Per n8n copia anche `N8N_ENCRYPTION_KEY` dal compose attuale. Una chiave
   diversa rende illeggibili le credenziali dei workflow.
3. Per Pi-hole copia la cartella `etc-pihole` dello stack (`/data/compose/<id>/etc-pihole`)
   in `/DATA/AppData/pihole` prima di cancellare lo stack.
4. Ruota il token del tunnel Cloudflare: era scritto in chiaro nel compose di
   `utils`. Il valore nuovo va in `CLOUDFLARED_TUNNEL_TOKEN`.
5. Rimuovi lo stack vecchio da *Stacks*. Non cancellare le cartelle in
   `/DATA/AppData` né i volumi `n8n_n8n-postgres`, `n8n_n8n-data`,
   `utils_filebrowser_*` e `pterodactyl_database_data`.
6. *Add stack* → *Repository* con il compose path della tabella e il nome
   indicato in *Nome su Portainer*. Incolla `stack.env.example` e sostituisci
   i segnaposto.

`media` usa la subnet `172.28.10.0/24`, `utils` usa `172.20.0.0/16`. Vanno
tenute diverse tra loro e diverse da `docker0`.

Gli stack dell'homelab non includono Watchtower. Si aggiornano con *Update the
stack*. L'unico Watchtower del repository è quello di `free-games-claimer` e
non tocca gli altri container.

## Sicurezza

Il repository è **pubblico**. Password, token e webhook vanno inseriti solo nelle
*Environment variables* di Portainer. Il `.gitignore` esclude ogni file `*.env`, ma
`stack.env.example` deve contenere esclusivamente valori di esempio.

## Errori comuni su Portainer

| Errore | Causa | Soluzione |
| --- | --- | --- |
| `additional properties '<nome>' not allowed` | Il servizio non è sotto la chiave `services:` | Usa il `docker-compose.yml` del repository così com'è |
| `open /data/compose/<n>/...docker-compose.yml: no such file or directory` | *Compose path* sbagliato | Percorso relativo alla root del repository, es. `free-games-claimer/docker-compose.yml` |
| `required variable <NOME> is missing a value` | Variabile mancante nelle *Environment variables* | Aggiungila (l'elenco completo è in `stack.env.example`) |
| `container name "/<nome>" is already in use` | Esiste ancora un container creato a mano o da un tentativo precedente | Eliminalo da *Containers* (i dati nella cartella di configurazione restano) e rifai il deploy |
| `Pool overlaps with other one on this address space` | Due reti Docker con la stessa subnet | `media` è `172.28.10.0/24` e `utils` è `172.20.0.0/16`. Non riusare una delle due per un altro stack |
