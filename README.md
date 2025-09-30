# vps-homelab

Personal homelab sandbox running on a VPS.

This README is mostly personal notes as a reminder for myself if I need to set up a new VPS with caddy as a reverse proxy and dockerized apps.

## Prerequisites

Not using ansible because I want to keep VPS bootstrapping as simple as possible. This is a guide for me if need to set up a new VPS.

### Security

#### SSH Hardening

1. Create a new user and add to sudo group
2. Set up SSH keys for the new user
3. Change SSH port using `sudo systemctl edit ssh.socket`
4. Disable root login and password authentication in `/etc/ssh/sshd_config`

#### Brute Force Protection

Using `fail2ban` to protect against brute force attacks with a simple local config for SSH retries under [sshd] config (no need to install with ubuntu 25.04)

#### Basic Firewall

Using ufw to set up a basic firewall ruleset (allow ssh, http, https).

```bash
# Only allow http, https and our custom ssh port
sudo ufw default deny incoming
sudo ufw default allow outgoing

sudo ufw allow $PORT_NUMBER/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### Install docker compose (since everything is containerized)

1. Install docker engine, cli and compose plugin, i.e. : <https://docs.docker.com/engine/install/ubuntu/>
2. Verify the docker service is running `sudo systemctl status docker`
3. Add user to docker group `sudo usermod -aG docker $USER`
4. Test docker installation with `docker run hello-world`

**Other useful tools for a good terminal exp:**

- lazydocker
- atuin
- starship
- tmux

### Root domain and CloudFlare DNS

1. Buy a domain from any registrar (CloudFlare directly OK) (Optional: Setup nameservers)
2. Add `A` record to point to VPS IP (no CloudFlare proxy, just DNS).
3. Add `CNAME` record for `www` to point to root domain (no CloudFlare proxy, just DNS).

## Structure

- Caddy as a reverse proxy and cert manager. Create a single caddy network for all containers to share. Stored in `~/infra/caddy/`
- Other apps in `~/apps/`. Will refer to caddy network for reverse proxying.
- Tools in `~/tools/`
- etc.

### Caddy healthcheck and restart

Docker compose file for caddy with a healthcheck and restart policy in `~/infra/caddy/docker-compose.yml`

```yaml
# Since caddy img does not have curl
healthcheck:
  test: ["CMD-SHELL", "wget -q -O - http://localhost/health | grep -q 'OK'"]
```

```caddy
# Port 80: healthcheck + redirect
:80 {
    respond /health "OK" 200
}
```

Verify its working outside the vps with `curl http://simlal.dev/health`

### Redirect http and www

Use simple redirects in caddyfile:

```caddy
:80 {
    respond /health "OK" 200
    redir https://simlal.dev{uri} permanent
}
simlal.dev {
    respond /health "HEALTHY" 200
    respond "Hello, world!"
}
```

## Apps

TODO
