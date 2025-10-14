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

Using Kuma service mesh for observability. Accessible via `kuma.simlal.dev` reverse proxied by Caddy.

### Container monitoring

Uptime Kuma dashboard for container monitoring.

- Main healthcheck `simlal.dev/health`
- Static site healthcheck `healthcheck.simlal.dev`
- Caddy container service
- ...
