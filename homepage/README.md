# Homepage

La bacheca. Una pagina sola che mostra cosa è acceso in casa: i container, lo
spazio su disco, i link alle altre stanze.

[Homepage](https://github.com/gethomepage/homepage) ascolta sulla porta
`WEBUI_PORT_HOMEPAGE`. In casa è la `80`.

Compose path: `homepage/docker-compose.yml`. Nome stack: `homepage`.

## Cosa tiene acceso

| Pezzo | Dove sta |
| --- | --- |
| Widget e `services.yaml` | `${FOLDER_FOR_CONFIGS}/homepage`, oggi `/DATA/AppData/homepage` |
| Stato dei container | il socket Docker. Chi apre la bacheca vede lo stato dei container |
| Dischi | `${FOLDER_FOR_HDD}` montato su `/hdd` |
| Host ammessi | `HOMEPAGE_ALLOWED_HOSTS`, nomi separati da virgole, senza spazi |

## Accenderla al posto di quella già in piedi

1. Incolla [`stack.env.example`](stack.env.example) e controlla gli host ammessi.
2. Rimuovi lo stack vecchio senza cancellare `/DATA/AppData/homepage`.
3. Deploy da git. Il container si chiama ancora `homepage`: se il vecchio è
   ancora lì, eliminalo da *Containers* e rilancia.

I widget che hai già disegnato restano nella cartella di config.
