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

### GitHub Actions Runner

Install a self-hosted GitHub Actions runner on the VPS to run CI/CD pipelines for deploying apps.
Follow GH instructions with install at `~/actions-runner` and run as a systemd service with

```bash
./svc.sh install
./svc.sh start

# Check status
sudo systemctl status actions.runner.<runner_name>.service
```

Will use this to build and deploy apps to the VPS.

### Setup CRON docker system prune

Set up a cron job to run `docker system prune -f` weekly to clean up unused docker resources.

```bash
crontab -e
# Add the following line to run every Sunday at 3am
0 3 * * 0 docker image prune -a -f
```

## Structure

VPS home directory structure from a non-root but sudo user:

- Caddy as a reverse proxy and cert manager. Create a single caddy network for all containers to share.
Stored in `~/infra` for volume mounts.
- Kuma for service mesh and observability. Part of caddy compose stack.
- Other apps in `~/apps/`. Will refer to caddy network for reverse proxying.
- Tools in `~/tools/`
- etc.

### Caddy

For testing, we can create a deploy key in GitHub, clone the repo to `~/infra_test/` and run caddy from there.

Later we use a CI/CD pipeline to deploy caddy config changes from GitHub Actions using our self-hosted runner.

#### caddy network

First create a docker network for caddy and other apps to share:

```bash
docker network create caddy_net
```

#### admin & healthcheck

Use caddy admin endpoint for healthcheck and restart if needed.

```yaml
# With admin enabled
healthcheck:
  test: ["CMD-SHELL", "wget -qS -O /dev/null http://localhost:2019/config"]
  interval: 30s
  timeout: 5s
  retries: 3
```

#### Redirect http and www

Use simple redirects in caddyfile to test setup

```caddy
:80 {
    redir https://simlal.dev{uri}
}
www.simlal.dev {
    redir https://simlal.dev{uri} permanent
}
simlal.dev {
    respond /health "HEALTHY" 200
    respond "Hello, world!"
}
```

#### Secrets

Managed with GH actions (could also add a .env file in compose and avoid override on rsync).
Made GH action compatible with `.env` file or secrets in caddy compose stack.

### Background services

- `actions.runner.<runner_name>.service` → self-hosted GitHub Actions runner installed as a systemd service (installed at `~/actions-runner`)
- `cron` → weekly docker image prune

## Apps

TODO
