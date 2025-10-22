# simlal.dev VPS Homelab

  My VPS homelab setup with simple docker-compose stacks and Caddy as a reverse proxy & https cert manager.

## Setup

See `GUIDE.md` for detailed setup instructions and security considerations.

## Architecture

Structure of VPS home directory from a non-root but sudo user:

- `~/infra/` - Caddy and Kuma docker-compose stacks
- `~/apps/` - Other apps
- `~/tools/` - Other tools
- `~/actions-runner/` - Self-hosted GitHub Actions runner as a systemd service

### Infra

- Caddy as a reverse proxy and cert manager. Create a single caddy network for all containers to share.
- GH Actions runners on VPS for CI/CD (self-hosted runners as systemd services)
- Kuma for observability. Accessible via Tailscale net OR `kuma.simlal.dev`
- TODO...

### Apps

- Static healthcheck: <a href="https://healthcheck.simlal.dev">healthcheck.simlal.dev</a>
- `curl`-able CV: TODO...
- ...

## Observability

### Container monitoring

Using Kuma service mesh for observability.
Accessible via <a href="https://kuma.simlal.dev/">kuma.simlal.dev</a> reverse proxied by Caddy.

Manually configure Kuma monitors for each app/container on dashboard.

Example:

### Caddy static file healthcheck

Caddy serves a static file at `healthcheck` subdomain:
<a href="http://healthcheck.simlal.dev">healthcheck.simlal.dev</a>.

### Notifications

TODO: Use Kuma alerts to send notifications to email/Slack/etc.

## GitHub Actions CI/CD

Using self-hosted GitHub Actions runners on the VPS for CI/CD.
Push-based model for simplicity (no webhooks or polling).

### Caddy + infra monitoring deployment

All stacks are deployed via GitHub Actions workflows in this repo.

### Apps deployment

Similar push-based GitHub Actions workflows for each app repo to deploy to VPS.
