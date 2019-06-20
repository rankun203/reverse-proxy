# Dev Traefik proxy for local development

```bash
docker network create web
docker-compose up -d
```

Then, in your docker app, append below labels:

```yml
    labels:
      - "traefik.enable=true"
      - "traefik.docker.network=web"
      - "traefik.backend=<service_name>"
      - "traefik.frontend.rule=Host:<local.domain.tld>"
      - "traefik.port=80"
```

Then, edit `/etc/hosts`, map `<local.domain.tld>` to `127.0.0.1` and navigate to `<local.domain.tld>`.