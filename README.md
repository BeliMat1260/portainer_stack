# portainer_stack

Stack Docker Compose da distribuire con Portainer (*Add stack* → *Repository*).
Ogni cartella è uno stack indipendente con il suo `docker-compose.yml`, un
`stack.env.example` con le variabili da impostare su Portainer e un README.

| Stack | Compose path |
| --- | --- |
| [free-games-claimer](free-games-claimer/) | `free-games-claimer/docker-compose.yml` |

Le credenziali non vanno mai committate: si inseriscono solo nella sezione
*Environment variables* dello stack su Portainer.
