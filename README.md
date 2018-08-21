# Dev Traefik proxy for local development

```bash
docker network create web
docker-compose up -d
```

Then, in your docker app, append below labels:

```yml
    labels:
      - "traefik.backend=service_name"
      - "traefik.docker.network=web"
      - "traefik.enable=true"
      - "traefik.frontend.rule=Host:<local.domain.tld>"
      - "traefik.port=80"
      - "traefik.protocol=http"
```

Then, edit `/etc/hosts`, map `<local.domain.tld>` to `127.0.0.1` and navigate to `<local.domain.tld>`.