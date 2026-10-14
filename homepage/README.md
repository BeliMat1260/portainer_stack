# Stack Portainer: homepage

[Homepage](https://github.com/gethomepage/homepage) sulla porta
`WEBUI_PORT_HOMEPAGE` (oggi 80). Compose path: `homepage/docker-compose.yml`.

La configurazione dei widget resta in `${FOLDER_FOR_CONFIGS}/homepage`
(`/DATA/AppData/homepage` sull'host attuale). Il socket Docker serve ai widget
che leggono lo stato dei container: chi raggiunge Homepage può vedere quei
dati. `HOMEPAGE_ALLOWED_HOSTS` è l'elenco di host accettati, separati da virgole,
senza spazi.

Per sostituire lo stack già attivo: rimuovilo da Portainer senza cancellare
quella cartella, poi fai il deploy da git. Il `container_name` è ancora
`homepage`.
