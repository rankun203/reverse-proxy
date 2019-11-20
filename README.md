# Reverse Proxy

Reverse Traefik proxy for local development & lightweight deployment

```bash
git clone https://github.com/rankun203/reverse-proxy.git
docker network create public
docker-compose up -d traefik
```

Then, in your docker compose service, append below labels:

```yml
    labels:
      - 'traefik.enable=true'
      - 'traefik.backend=<service-name>'
      - 'traefik.frontend.rule=Host:<local.domain.tld>'
      - 'traefik.port=80'
```

## Local Development

Just use `*.localtest.me`, for example `chat.localtest.me`, `proj.localtest.me`, etc, they all pointing to `127.0.0.1`.

## Deployment

In order to get https working, uncomment these lines of code in traefik.toml (and use a proper email):

```toml
# [acme]
# email = "acme@rankun.net"
# storage = "/etc/traefik/acme.json"
# entryPoint = "https"
# onHostRule = true
# 
# [acme.httpChallenge]
# entryPoint = "http"
```

If you want to automatically redirect http traffic to https, uncomment these lines as well:

```toml
    # [entryPoints.http.redirect]
    # entryPoint = "https"
```
