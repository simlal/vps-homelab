# simlal.dev VPS Homelab

  My VPS homelab setup with simple docker-compose stacks and Caddy as a reverse proxy & https cert manager.

## Setup

See `GUIDE.md` for detailed setup instructions and security considerations.

## Architecture

Structure of VPS home directory from a non-root but sudo user:

- `~/infra/` - Caddy and Kuma docker-compose stacks
- `~/apps/` - Other apps
- `~/tools/` - Other tools

### Infra

- Caddy as a reverse proxy and cert manager. Create a single caddy network for all containers to share.
- GH Actions runners on VPS for CI/CD (self-hosted runners as systemd services)
- Kuma for observability. Accessible via Tailscale OR `kuma.simlal.dev`
- TODO...

### Apps

TODO

## Observability

### Container monitoring

Using Kuma service mesh for observability. Accessible via <a href="https://kuma.simlal.dev/">kuma.simlal.dev</a> reverse proxied by Caddy.

### Caddy static file healthcheck

Caddy serves a static file at `healthcheck` subdomain: <a href="http://healthcheck.simlal.dev">healthcheck.simlal.dev</a>
