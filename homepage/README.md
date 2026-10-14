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

## Widget

I widget si configurano in `/DATA/AppData/homepage/services.yaml`. Dopo aver salvato il file, riavvia il container `homepage`.

Homepage non è sulle reti degli altri stack: nei widget `url` è sempre l'IP del server con la porta pubblicata.

### Pi-hole

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
| `url` | Porta `8081`, senza `/admin`. Pi-hole usa la rete dell'host, quindi `http://pihole` non si risolve da Homepage |
| `version` | `6`. Senza, Homepage usa la vecchia API `/admin/api.php` |
| `key` | Il valore di `PIHOLE_PASSWORD` dello stack [pihole](../pihole/) |

| Errore del widget | Causa |
| --- | --- |
| `Pi-Hole API Error` | Manca `key`, oppure `url` non raggiunge Pi-hole |
| `Failed to authenticate with Pi-hole` | `key` diversa dalla password di Pi-hole |

### Jellyfin

```yaml
- Jellyfin:
    icon: jellyfin.svg
    href: http://192.168.1.90:8096
    description: Media server
    widget:
      type: jellyfin
      url: http://192.168.1.90:8096
      key: JELLYFIN_API_KEY
      version: 2
      enableBlocks: true
```

| Campo | Valore |
| --- | --- |
| `url` | Porta `8096` dello stack [media](../media/) |
| `version` | `2`. Jellyfin 12 (10.12) ha tolto `/emby/...` e `?api_key=`, che Homepage usa con la versione `1`, quella predefinita |
| `key` | Chiave creata in Jellyfin: *Dashboard* → *API Keys* (`http://192.168.1.90:8096/web/#/dashboard/keys`) |

| Errore del widget | Causa |
| --- | --- |
| `JSON.parse: unexpected end of data` (Firefox) o `Unexpected end of JSON input` (Chrome) | Manca `version: 2`, oppure `key` non è valida. Jellyfin risponde 404 o 401 senza corpo |
