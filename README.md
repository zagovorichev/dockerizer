# Docker helpers

## Traefik

## MkCert

To use secured https:
https://github.com/FiloSottile/mkcert

## Instruction

### Configure DNSMasq
https://guewen.github.io/blog/2018/04/12/docker-traefik-and-dnsmasq/

### Create network
`docker network create traefik-public`

### Create certificates
- `cd docker/traefik/certs`
- `mkcert -install`
- `mkcert "*.docker.localhost"`

### Run env
```
docker-compose up -d
```

### Resources
- https://traefik.docker.localhost/dashboard/#/
- https://portainer.docker.localhost/#/auth
- https://whoami.docker.localhost/