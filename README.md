# portainer_stack

Raccolta di stack Docker Compose per l'home server, pensati per essere distribuiti con
**Portainer** direttamente da questo repository Git (*Stacks* → *Add stack* → *Repository*).

Ogni cartella è uno stack indipendente: si distribuisce, si aggiorna e si rimuove senza
toccare gli altri.

## Stack disponibili

| Stack | Descrizione | Compose path |
| --- | --- | --- |
| [free-games-claimer](free-games-claimer/) | Riscatta in automatico i giochi gratis (Epic, Prime Gaming, GOG, Ubisoft, Fab, AliExpress) con aggiornamento automatico dell'immagine | `free-games-claimer/docker-compose.yml` |

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
| `Pool overlaps with other one on this address space` | Due reti Docker con la stessa subnet | Non fissare la subnet nelle reti degli stack, lascia che la scelga Docker |
