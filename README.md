# simlal.dev VPS Homelab

  My VPS homelab setup with simple docker-compose stacks with Caddy as a reverse proxy.

## Setup

See `GUIDE.md` for detailed setup instructions and security considerations.

## Architecture

- Caddy as a reverse proxy and cert manager. Create a single caddy network for all containers to share.
- GH Actions runners on VPS for CI/CD (self-hosted runners as systemd services)
- Kuma for observability. Accessible only via Tailscale IP/MagicDNS
- TODO
-
