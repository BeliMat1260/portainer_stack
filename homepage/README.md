# Homepage

Pagina unica con lo stato dei container, lo spazio disco e i link agli altri servizi. Il progetto è [gethomepage/homepage](https://github.com/gethomepage/homepage).

| | |
| --- | --- |
| Progetto | `homepage` |
| Compose | [docker-compose.yml](docker-compose.yml) |
| Variabili | [stack.env.example](stack.env.example) |
| Configurazione | [config/](config/) |
| Porta sull'host | `80` |

[Installazione](../README.md#installazione).

## Configurazione

Homepage legge i file da `/DATA/AppData/homepage`, non dal repository. Dopo ogni modifica a [config/](config/) vanno ricopiati sul server:

```bash
cd /DATA/AppData/homepage
for f in services settings widgets; do
  curl -fsSLO "https://raw.githubusercontent.com/BeliMat1260/portainer_stack/main/homepage/config/$f.yaml"
done
```

Homepage rilegge i file da solo, senza riavviare il container.

I file non contengono chiavi. Ogni `{{HOMEPAGE_VAR_…}}` viene sostituito con la variabile omonima delle *Environment variables* dello stack, senza virgolette e senza `'`.

| Variabile | Dove si trova |
| --- | --- |
| `HOMEPAGE_VAR_JELLYFIN_KEY` | Jellyfin → *Dashboard* → *API Keys* |
| `HOMEPAGE_VAR_SEERR_KEY` | Seerr → *Settings* → *General* → *API Key* |
| `HOMEPAGE_VAR_RADARR_KEY`, `HOMEPAGE_VAR_SONARR_KEY`, `HOMEPAGE_VAR_PROWLARR_KEY` | *Settings* → *General* → *API Key* |
| `HOMEPAGE_VAR_QBITTORRENT_USER`, `HOMEPAGE_VAR_QBITTORRENT_PASSWORD` | Login della WebUI di qBittorrent |
| `HOMEPAGE_VAR_GLUETUN_KEY` | Lo stesso valore di `GLUETUN_API_KEY` dello stack [media](../media/) |
| `HOMEPAGE_VAR_PIHOLE_KEY` | Password di Pi-hole, oppure *Settings* → *Web interface / API* → *App password* |
| `HOMEPAGE_VAR_NEXTCLOUD_URL` | Indirizzo pubblico di Nextcloud, per esempio `https://nextcloud.example.com` |
| `HOMEPAGE_VAR_NEXTCLOUD_TOKEN` | Nextcloud → *Impostazioni di amministrazione* → *Sistema* → token `NC-Token` |
| `HOMEPAGE_VAR_SYNCTHING_KEY` | Syncthing → *Azioni* → *Impostazioni* → *Generale* → *Chiave API* |
| `HOMEPAGE_VAR_PORTAINER_ENV` | Il numero alla fine dell'URL dell'ambiente, `#!/3/docker/…` → `3` |
| `HOMEPAGE_VAR_PORTAINER_KEY` | Portainer → *My account* → *Access tokens* |
| `HOMEPAGE_VAR_GITEA_KEY` | Gitea → *Impostazioni* → *Applicazioni* → token di accesso |

## Dati

| Contenuto | Percorso |
| --- | --- |
| Widget e configurazione | `/DATA/AppData/homepage` |
| Stato dei container | socket Docker, in sola lettura |
| Dischi | `/hdd` |
| Host accettati | `HOMEPAGE_ALLOWED_HOSTS`, separati da virgole e senza spazi |
