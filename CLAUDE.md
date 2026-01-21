# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**lich** ("lucy's infrastructure code home") is a single-node Ansible-managed infrastructure for a personal webserver at luuucyyy.net. The stack consists of three layers:
- **Tailscale**: VPN network overlay and exit node
- **OpenResty**: Nginx + Lua reverse proxy with automatic ACME/Let's Encrypt SSL
- **Docker**: Container runtime for application services

## Commands

```bash
# Run the full playbook (requires HCLOUD_TOKEN env var)
ansible-playbook main.yml --vault-pass-file path/to/secret

# Test connectivity only
ansible-playbook check.yml --vault-pass-file path/to/secret

# Run specific roles via tags
ansible-playbook main.yml --vault-pass-file path/to/secret --tags docker
ansible-playbook main.yml --vault-pass-file path/to/secret --tags nginx
ansible-playbook main.yml --vault-pass-file path/to/secret --tags tailscale

# Lint
ansible-lint
```

## Architecture

The main playbook (`main.yml`) imports three roles in order:
1. `roles/tailscale/` - Installs and authenticates Tailscale
2. `roles/openresty/` - Installs OpenResty, configures reverse proxy routes, sets up SSL certificates, creates health endpoint
3. `roles/docker/` - Installs Docker via snap, authenticates to GHCR, pulls and runs containers

## Adding Services

Services are defined declaratively in `config.yml`:

```yaml
containers:
  - subdomain: myapp     # Available at myapp.luuucyyy.net (use false for root domain)
    name: myapp          # Container name
    image: ghcr.io/lucykowal/myapp:main
    container_port: "80" # Port app listens on inside container
    host_port: "8082"    # Unique port exposed on localhost
```

Both `subdomain` and `host_port` must be unique across all containers.

## Secrets

All secrets are Ansible Vault encrypted in `config.yml`:
- `vault_github_username` / `vault_github_token` - GHCR authentication
- `vault_certbot_email` - ACME certificate email
- `vault_tailscale_key` - Tailscale auth key

The vault password file and `HCLOUD_TOKEN` should never be committed.

## Inventory

Uses Hetzner Cloud dynamic inventory (`inventory/hcloud.yml`). The `HCLOUD_TOKEN` environment variable must be set.
