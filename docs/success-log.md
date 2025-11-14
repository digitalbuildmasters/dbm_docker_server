# Success Log (2025-11-14)

```
[2025-11-14T07:47:07Z] Baseline diagnostics captured (OS, kernel, CPU/RAM, storage, network, DNS, ports, Docker status).
[2025-11-14T07:55:00Z] Confirmed decision to stay on Ubuntu 24.04.3 LTS for this VPS.
[2025-11-14T08:20:00Z] Created 'sysadmin' user with home directory and locked password.
[2025-11-14T08:21:00Z] Added required groups: dbm-ops (gid 9000) and docker (gid 9001 as 999 already in use by systemd-journal).
[2025-11-14T08:22:00Z] Added 'sysadmin' to sudo, dbm-ops, docker groups (id sysadmin confirms memberships).
[2025-11-14T08:40:00Z] Installed chrony, tree, tzdata-legacy; enabled chrony service.
[2025-11-14T08:42:00Z] Applied DBM sysctl policy (ip_forward=1, bridge-nf-call-iptables=1) and loaded br_netfilter.
[2025-11-14T08:50:00Z] Added Docker apt repo, installed docker-ce stack (29.0.0), Docker Compose v2.40.3, containerd 2.1.5.
[2025-11-14T08:55:00Z] Created /opt/dbm_docker and /opt/dbm_PE directory tree with configs/logs/secrets/volumes/apps etc., set sysadmin ownership plus secret permissions.
[2025-11-14T09:10:00Z] Verified Docker daemon (29.0.0), containerd, chrony sync, swap disabled, ports 80/81/443 free.
[2025-11-14T09:12:00Z] Audited /opt/dbm_docker and /opt/dbm_PE structures; enforced secrets subdirectories at 700 perms.
[2025-11-14T09:30:00Z] Deployed Odoo19 Enterprise via /opt/dbm_docker/scripts/odoo19_enterprise_setup.sh (logs: /opt/dbm_PE/logs/odoo19_setup_$(date -u +%Y%m%dT%H%M%SZ).log). Containers dbm_odoo19_db + dbm_odoo19_app running with ZATCA fix and pgvector enabled.
[2025-11-14T09:31:00Z] Final Odoo19 setup log archived at /opt/dbm_PE/logs/odoo19_setup_20251114T082720Z.log (previous attempts kept for traceability).
[2025-11-14T09:40:00Z] Deployed Nginx Proxy Manager (dbm_npm_ce) via /opt/dbm_docker/apps/nginx-proxy-manager/docker-compose.yml, mapped ports 80/81/443, env file /opt/dbm_docker/secrets/nginx-proxy-manager/.env, volumes under /opt/dbm_docker/volumes/nginx-proxy-manager.
[2025-11-14T08:39:55Z] Rotated NPM admin credentials (sysadmin@dbm.com.sa) via /opt/dbm_docker/secrets/nginx-proxy-manager/.env and force-recreated dbm_npm_ce to apply.
[2025-11-14T11:15:06Z] Added dbm_odoo19_nginx service with upstream config, attached to dbm_reverse_proxy, and pointed NPM proxy host + SSL to it for dbm.com.sa.
[SUCCESS][UTC 2025-11-14T11:40:18Z | AST 2025-11-14T14:40:18+0300] Odoo19 ZATCA QR timestamp fix deployed. Methodology: backed up /usr/lib/python3/dist-packages/odoo/addons/l10n_sa/models/account_move.py inside dbm_odoo19_app, copied patches/account_move_zatca_fix.py over it, restarted docker compose odoo service, and verified timedelta(hours=3) plus clean post-restart logs.
[SUCCESS][UTC 2025-11-14T12:22:32Z | AST 2025-11-14T15:22:32+0300] n8n + NPM SSL hardening complete. Issued Let's Encrypt certs for n8n.dbm.com.sa (id=4) and pxy.dbm.com.sa (id=5) via NPM API, bound hosts 2/3 to new certs, verified HTTPS locally with curl --resolve, and confirmed n8n secure cookie warning resolved.
[SUCCESS][UTC 2025-11-14T12:40:00Z | AST 2025-11-14T15:40:00+0300] Gitea stack deployed via /opt/dbm_docker/apps/gitea/docker-compose.yml (services dbm_gitea_db + dbm_gitea_app) with env file /opt/dbm_docker/secrets/gitea/gitea.env, volumes under /opt/dbm_docker/volumes/gitea, and admin user dbmadmin recorded in /opt/dbm_docker/secrets/gitea/admin_credentials.txt.
[SUCCESS][UTC 2025-11-14T12:43:00Z | AST 2025-11-14T15:43:00+0300] Added NPM proxy host for gita.dbm.com.sa, issued Let's Encrypt certificate id=6, forced SSL, and verified HTTPS with curl --resolve gita.dbm.com.sa:443:127.0.0.1 https://gita.dbm.com.sa showing HTTP/2 200.
[SUCCESS][UTC 2025-11-14T12:50:00Z | AST 2025-11-14T15:50:00+0300] Bootstrapped /opt/dbm_docker/dbm_docker_server git repo (README, docs/success-log.md, docs/deployment_playbook.md) to centralize methodology, synced entries from /opt/logs/dbm_success_2025-11-14.log, and documented next publishing steps to Gitea + GitHub.
[SUCCESS][UTC 2025-11-14T12:54:00Z | AST 2025-11-14T15:54:00+0300] Added /opt/dbm_docker/dbm_docker_server/docs/index.md plus README pointers so every `/opt` directory, compose file, and secret location is indexed; success log synchronized with canonical file.
```
