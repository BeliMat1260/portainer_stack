# Homepage

La bacheca della casa: container, spazio disco e link agli altri servizi.
Il progetto è [Homepage](https://github.com/gethomepage/homepage).

Nome su Portainer: `homepage`.
Compose path: `homepage/docker-compose.yml`.
Porta: `WEBUI_PORT_HOMEPAGE` (`80`).

| Pezzo | Dove sta |
| --- | --- |
| Widget e configurazione | `${FOLDER_FOR_CONFIGS}/homepage` (`/DATA/AppData/homepage`) |
| Stato dei container | socket Docker |
| Dischi | `${FOLDER_FOR_HDD}` (`/hdd`) montato su `/hdd` |
| Host accettati | `HOMEPAGE_ALLOWED_HOSTS`, separati da virgole, senza spazi |

Il modello delle variabili è [`stack.env.example`](stack.env.example).
