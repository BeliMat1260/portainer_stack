# Pi-hole

DNS della rete locale. Il container usa la rete dell'host e ascolta la porta `53`. L'interfaccia web è sulla porta `8081`.

| | |
| --- | --- |
| Progetto | `pihole` |
| Compose | [docker-compose.yml](docker-compose.yml) |
| Variabili | [stack.env.example](stack.env.example) |
| Rete | rete dell'host |

[Installazione](../README.md#installazione).

## Configurazione

| | |
| --- | --- |
| Password della web UI | `PIHOLE_PASSWORD` |
| DNS a monte | `1.1.1.1;9.9.9.9`, fissi nel compose |
| Fuso | `Europe/Rome` |
| Dati | `/DATA/AppData/pihole` |

L'API (`/api`) chiede la stessa password della web UI. Per il widget di Homepage vedi [homepage](../homepage/README.md#pi-hole).

## Modem e loop DNS

Se il modem usa Pi-hole come DNS e Pi-hole usa il modem, le query rimbalzano tra i due. Pi-hole accetta al massimo 1000 query al minuto dal modem, poi le rifiuta: tutta la rete resta senza DNS e il modem è sommerso di richieste.

Il compose forza queste impostazioni, anche se il `pihole.toml` copiato da una vecchia installazione dice altro. Nella web UI risultano bloccate.

| Impostazione | Valore | Perché |
| --- | --- | --- |
| DNS a monte | `1.1.1.1;9.9.9.9` | Mai `192.168.1.1` |
| Inoltro condizionale | spento | Girerebbe al modem i nomi locali e le ricerche inverse |
| DHCP | spento | Il DHCP resta sul modem, che dopo un reset è di nuovo acceso |
| Ascolto | `local` | Risponde solo ai dispositivi di casa, anche con DMZ o porte aperte sul modem |

La regola per il modem TIM (`192.168.1.1`): può usare Pi-hole (`192.168.1.90`) come DNS, ma Pi-hole non deve mai usare il modem.

- Meglio mettere `192.168.1.90` come DNS nel DHCP del modem: Pi-hole vede ogni dispositivo e il limite di 1000 query al minuto vale per dispositivo.
- Se invece è il modem a girare le query a Pi-hole, nel registro compare solo `192.168.1.1` e il limite vale per tutta la casa.
- La porta `53` non va aperta verso internet.

Se Pi-hole è l'unico DNS della rete, ogni volta che lo stack si ferma (aggiornamento, riavvio del server) la casa resta senza DNS. Anche il server ne risente se risolve i nomi passando dal modem: Docker non riesce a scaricare l'immagine di Pi-hole proprio mentre Pi-hole è spento. Sul server conviene impostare un DNS fisso esterno, per esempio `1.1.1.1`, al posto di quello del modem.

Senza inoltro condizionale il registro delle query mostra gli IP al posto dei nomi. I nomi si aggiungono a mano in *Settings* → *Local DNS Records*.

Per usare il DHCP di Pi-hole bisogna prima spegnere quello del modem, poi togliere `FTLCONF_dhcp_active` dal compose.

### Se la rete si blocca

1. In Portainer ferma lo stack `pihole`.
2. Nel modem riporta il DNS automatico al posto di `192.168.1.90`, poi riavvia il modem. Il reset di fabbrica non serve.
3. Ridistribuisci lo stack con questo compose e rimetti `192.168.1.90` nel modem.
