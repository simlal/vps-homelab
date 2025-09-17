# vps-homelab-k8s

Personal Kubernetes homelab sandbox running on a single VPS.

This README is mostly personal notes as a reminder for myself if I need to set up a new VPS + k3s cluster.

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

Using ufw to set up a basic firewall ruleset (allow ssh, http, https). Do not allow Kubernetes ports from outside but use ssh port forwarding instead.

```bash
sudo ufw delete allow 6443/tcp # k3s apiserver, use ssh port forwarding instead
```

### Install other dependencies for k3s setup

Ubuntu 25.04 comes already with `htop`, `curl`, `ufw`, `fail2ban`, `git`, `vim`. No need for dotfiles, just scp your .vimrc + install `yazi` for good navigation leveraging snap.

Just install k3s with the isntall script (single node setup):

```bash
curl -sfL https://get.k3s.io | sh -

# Check if OK
sudo k3s kubectl get nodes
```

We can also check if our `ufw` rules are working by curl-ing the apiserver port from outside:

```bash
# With ssh port forwarding ON + k3s.yaml scp-ed locally
curl -vk https://<your_vps_ip>:6443 # We should get a 401

kubectl get nodes # Should work
```

## Post Install

TODO

## Apps

TODO
