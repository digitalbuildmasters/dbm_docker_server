# dbm_docker_server

Production notes for the Digital Build Masters Docker host that runs Odoo19, internal nginx, NPM, n8n, and the newly deployed Gitea stack. This repository is a single place to keep the hardening steps, verification commands, and release notes that were captured in `/opt/logs/dbm_success_2025-11-14.log` on the server.

## Contents

- `README.md`: High-level overview (this file)
- `docs/success-log.md`: Chronological log copied from `/opt/logs/dbm_success_2025-11-14.log`
- `docs/deployment_playbook.md`: Playbook with clear steps for each successful deployment
- `docs/index.md`: Up-to-date map of `/opt` directories, services, and entry points
- `docs/todo.md`: Consolidated backlog for outstanding infrastructure work

## Environment Summary

- **Host OS**: Ubuntu 24.04.3 LTS (chrony for NTP, swap disabled, ip_forward=1)
- **Container Runtime**: Docker CE 29.0.0 + Compose v2.40.3
- **Networks**: `dbm_reverse_proxy` shared between NPM, Odoo19 nginx, n8n, and Gitea
- **Secrets Layout**: `/opt/dbm_docker/secrets/<app>` with 640 perms

## Versioning approach

1. Every time we finish a deployment or hardening task, append a `[SUCCESS]` line to `/opt/logs/dbm_success_<date>.log`.
2. Sync that log into `docs/success-log.md` and commit with the tag `deploy-YYYYMMDD.<n>`.
3. Keep the playbook updated using the same structure (Preparation → Execution → Verification) so new engineers can rerun the steps deterministically.

## Latest Highlights (Nov 14, 2025)

- ZATCA QR timestamp fix deployed to `dbm_odoo19_app`
- Internal nginx service added for Odoo19 and fronted by NPM with LE cert `npm-2`
- n8n stack deployed with Postgres + Redis, hardened basic-auth, and Let's Encrypt cert on `n8n.dbm.com.sa`
- `pxy.dbm.com.sa` SSL issued and points to NPM admin panel
- Gitea stack running at `https://gita.dbm.com.sa` behind NPM
- Infrastructure index (`docs/index.md`) added so every path under `/opt` is inventoried and easy to audit
- Task tracker (`docs/todo.md`) highlights Portainer exposure + automation items to keep future work visible

## Next Steps

- Push this repository to both the on-prem Gitea instance and GitHub `digitalbuildmasters/dbm_docker_server`
- Continue appending new successes and playbook entries as we harden additional services
