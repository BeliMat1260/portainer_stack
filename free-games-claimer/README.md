# Stack Portainer: free-games-claimer

Stack con [free-games-claimer-remaster](https://github.com/p-adamiec/free-games-claimer-remaster)
e un'istanza dedicata di [Watchtower](https://github.com/nicholas-fedor/watchtower) che
riscarica automaticamente l'immagine `:latest` e ricrea il container quando esce un update.

## Come funziona il repull

- **Automatico:** Watchtower controlla ogni giorno alle 04:00 (`WATCHTOWER_SCHEDULE`) se su
  `ghcr.io` c'è una nuova versione. Se sì, la scarica, ricrea `free-games-claimer` con gli
  stessi volumi, porte e variabili, e cancella la vecchia immagine.
  Watchtower agisce solo sui container con label `com.centurylinklabs.watchtower.scope=free-games-claimer`,
  quindi non tocca gli altri stack.
- **Manuale:** `pull_policy: always` fa riscaricare l'immagine ogni volta che premi
  *Update the stack* / *Pull and redeploy* su Portainer.

## La configurazione resta

Sessioni di login, cookie e database dei giochi già riscattati sono in `/fgc/data`, montato su
`${FOLDER_FOR_CONFIGS}/free-games-claimer` dell'host. Il container può essere ricreato quante
volte vuoi senza perdere nulla. Non cancellare quella cartella.

## Deploy su Portainer

1. *Stacks* → *Add stack* → *Repository*.
2. Repository URL: `https://github.com/BeliMat1260/portainer_stack`,
   reference `refs/heads/main`. Se il repository è privato, attiva *Authentication* e usa
   un Personal Access Token GitHub con permesso di lettura sul repository.
3. Compose path: `free-games-claimer/docker-compose.yml`.
4. *Environment variables* → *Advanced mode*: incolla il contenuto di `stack.env.example`
   e sostituisci i segnaposto con i valori veri (senza virgolette).
5. (Opzionale) *GitOps updates* → *Polling*: Portainer ridistribuisce lo stack quando cambi
   il file nel repository.
6. *Deploy the stack*.

Le credenziali vanno messe **solo** nelle variabili di Portainer: non committare mai un file
`stack.env` con le password vere.

Interfaccia noVNC (login manuale / captcha): `http://SERVER_IP:WEBUI_PORT_FREE_GAMES_CLAIMER`.
