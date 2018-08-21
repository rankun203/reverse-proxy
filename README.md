# Dev Traefik proxy for local development

```
docker network create web
docker-compose up -d
```

Then, in your docker app, append below labels:

```yml
    labels:
      - "traefik.backend=service_name"
      - "traefik.docker.network=web"
      - "traefik.enable=true"
      - "traefik.frontend.rule=Host:local.domain.tld"
      - "traefik.port=80"
      - "traefik.protocol=http"
```