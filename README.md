# Reverse Proxy

Reverse Traefik proxy for local development & lightweight deployment

### 1. Run Traefik

```bash
touch acme.json

docker network create public

docker run -d \
  --name=traefik \
  --network=public \
  -p 80:80 \
  -p 443:443 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $PWD/acme.json:/acme.json \
  traefik \
  --providers.docker \
  --providers.docker.exposedbydefault=false \
  --entrypoints.web.address=:80 \
  --entrypoints.websecure.address=:443 \
  --certificatesresolvers.myresolver.acme.email=spsacme@rankun.net \
  --certificatesresolvers.myresolver.acme.storage=/acme.json \
  --certificatesresolvers.myresolver.acme.tlschallenge=true \
  --accesslog=true
```

### 2. Integrate your service

Use necessary labels:

```yaml
version: "3.8"
networks:
  # docker network create public
  public:
    external: true
services:
  api:
    networks:
      - public
      - default
    labels:
      - "traefik.enable=true"
      - 'traefik.http.routers.customservice.rule=Host("api.2fools.app")'
      - 'traefik.http.services.customservice.loadbalancer.server.port=8000'
      - "traefik.http.routers.customservice.tls.certresolver=myresolver"
```

## Local Development

Just use `*.localtest.me`, for example `chat.localtest.me`, `proj.localtest.me`, etc, they all pointing to `127.0.0.1`.
