# Dev Traefik proxy for local development

```bash
docker network create public
docker-compose up -d traefik
```

Then, in your docker app, append below labels:

```yml
    labels:
      - "traefik.enable=true"
      - "traefik.docker.network=public"
      - "traefik.backend=<service_name>"
      - "traefik.frontend.rule=Host:<local.domain.tld>"
      - "traefik.port=80"
```

Then, edit `/etc/hosts`, map `<local.domain.tld>` to `127.0.0.1` and navigate to `<local.domain.tld>` (or use dnsmasq to map all .localhost domains to 127.0.0.1).
