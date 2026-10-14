# Homepage

Pagina unica con lo stato dei container, lo spazio disco e i link agli altri servizi. Il progetto è [gethomepage/homepage](https://github.com/gethomepage/homepage).

| | |
| --- | --- |
| Progetto | `homepage` |
| Compose | [docker-compose.yml](docker-compose.yml) |
| Variabili | [stack.env.example](stack.env.example) |
| Porta sull'host | `80` |

[Installazione](../README.md#installazione).

## Dati

| Contenuto | Percorso |
| --- | --- |
| Widget e configurazione | `/DATA/AppData/homepage` |
| Stato dei container | socket Docker |
| Dischi | `/hdd` |
| Host accettati | `HOMEPAGE_ALLOWED_HOSTS`, separati da virgole e senza spazi |
