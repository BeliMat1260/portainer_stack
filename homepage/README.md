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

## Widget Pi-hole

In `/DATA/AppData/homepage/services.yaml`:

```yaml
- Pi-hole:
    icon: pi-hole.svg
    href: http://192.168.1.90:8081/admin
    description: Network-wide ad blocker
    widget:
      type: pihole
      url: http://192.168.1.90:8081
      version: 6
      key: PIHOLE_PASSWORD
```

| Campo | Valore |
| --- | --- |
| `url` | IP del server e porta `8081`, senza `/admin`. Pi-hole usa la rete dell'host, quindi `http://pihole` non si risolve da Homepage |
| `version` | `6`. Senza, Homepage usa la vecchia API `/admin/api.php` |
| `key` | Il valore di `PIHOLE_PASSWORD` dello stack [pihole](../pihole/) |

Dopo aver salvato il file, riavvia il container `homepage`.

| Errore del widget | Causa |
| --- | --- |
| `Pi-Hole API Error` | Manca `key`, oppure `url` non raggiunge Pi-hole |
| `Failed to authenticate with Pi-hole` | `key` diversa dalla password di Pi-hole |
