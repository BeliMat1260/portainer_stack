# Stack Portainer: pterodactyl

Pannello Pterodactyl, MariaDB 10.5, Redis e Wings. Compose path:
`pterodactyl/docker-compose.yml`. Nome stack su Portainer: `pterodactyl`,
così il volume `pterodactyl_database_data` resta quello già creato.

Le password del database non sono più scritte nel compose. Nel file precedente
i segnaposto erano `password_segreta_db` e `password_segreta_root`: se il
database è già stato inizializzato con quelli, vanno rimessi identici in
`PTERO_DB_PASSWORD` e `PTERO_DB_ROOT_PASSWORD`. Una password nuova non entra
in un volume MySQL già creato.

`PTERO_APP_URL` è l'URL con cui apri il pannello, porta inclusa. Wings parla
con Docker tramite il socket dell'host: va trattato come un servizio di
amministrazione, non esposto su internet senza un proxy.

I volumi nominati `panel_data` e `wings_config` del compose vecchio non erano
montati da nessun servizio. Non sono stati ridichiarati. I dati veri del
pannello sono in `${FOLDER_FOR_CONFIGS}/pterodactyl/panel` e la config di
Wings in `${FOLDER_FOR_CONFIGS}/pterodactyl/wings`.

`restart` è `unless-stopped` al posto di `always`: fermare il container da
Portainer lo lascia fermo. Nessun Watchtower, l'immagine `:latest` del pannello
si aggiorna solo con *Update the stack*.
