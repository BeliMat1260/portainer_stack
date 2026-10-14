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
| DNS a monte | `PIHOLE_UPSTREAMS` · `1.1.1.1;9.9.9.9` |
| Fuso | `Europe/Rome` |
| Dati | `/DATA/AppData/pihole` |

L'API (`/api`) chiede la stessa password della web UI. Per il widget di Homepage vedi [homepage](../homepage/README.md#pi-hole).
