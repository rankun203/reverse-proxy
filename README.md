# Reverse Proxy

Reverse Traefik proxy for local development & lightweight deployment

### 1. Run Traefik

```bash
touch acme.json
chmod 600 acme.json

docker network create public

docker run -d \
  --name=traefik \
  --network=public \
  --restart=always \
  -p 80:80 \
  -p 443:443 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $PWD/acme.json:/acme.json \
  traefik \
  --providers.docker \
  --providers.docker.exposedbydefault=false \
  --providers.docker.network=public \
  --entrypoints.web.address=:80 \
  --entrypoints.websecure.address=:443 \
  --certificatesresolvers.myresolver.acme.email=spsacme@rankun.net \
  --certificatesresolvers.myresolver.acme.storage=/acme.json \
  --certificatesresolvers.myresolver.acme.tlschallenge=true \
  --accesslog=true
```

Notes:

1. `--providers.docker.network=public` is important, when target service is reachable through multiple networks, Traefik needs to know which one to send request.

Or with dashboard

```bash
sudo docker run -d \
  --name=traefik \
  --network=public \
  --restart=always \
  -p 80:80 \
  -p 443:443 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $PWD/acme.json:/acme.json \
  --label 'traefik.enable=true' \
  --label 'traefik.http.routers.mydashboard.rule=Host("traefik.website.com")' \
  --label 'traefik.http.routers.mydashboard.tls.certresolver=myresolver' \
  --label 'traefik.http.routers.mydashboard.service=api@internal' \
  --label 'traefik.http.middlewares.dashauth.basicauth.users=user:$apr1$sdsdf' \ # generated using htpasswd -nb user pass
  --label 'traefik.http.routers.mydashboard.middlewares=dashauth' \
  traefik \
  --providers.docker \
  --providers.docker.exposedbydefault=false \
  --providers.docker.network=public \
  --entrypoints.web.address=:80 \
  --entrypoints.websecure.address=:443 \
  --certificatesresolvers.myresolver.acme.email=degacme@rankun.net \
  --certificatesresolvers.myresolver.acme.storage=/acme.json \
  --certificatesresolvers.myresolver.acme.tlschallenge=true \
  --entrypoints.web.http.redirections.entrypoint.to=websecure \
  --entryPoints.web.http.redirections.entrypoint.scheme=https \
  --api.dashboard=true \
  --accesslog=true
```

### 2. Integrate your service

#### Using Docker compose

This config exposes the api service to public through https://api.2fools.app

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
      # Optional basic auth, generated using: htpasswd -nb user pass
      - "traefik.http.middlewares.customservice-auth.basicauth.users=${HTPASSWD_NB_USER_PASS}"
      - "traefik.http.routers.customservice.middlewares=customservice-auth"

```

#### Using Docker run

This command starts a Caddy server and serve files from current folder to public through https://service.sps.mindfine.com

```bash
docker run -d --name caddy_server --restart always \
    -v $(pwd):/usr/share/caddy \
    --network public \
    --label "traefik.enable=true" \
    --label 'traefik.http.routers.customservice.rule=Host(`service.sps.mindfine.com`)' \
    --label 'traefik.http.services.customservice.loadbalancer.server.port=80' \
    --label "traefik.http.routers.customservice.tls.certresolver=myresolver" \
    caddy
```

## Local Development

Just use `*.localtest.me`, for example `chat.localtest.me`, `proj.localtest.me`, etc, they all pointing to `127.0.0.1`.
