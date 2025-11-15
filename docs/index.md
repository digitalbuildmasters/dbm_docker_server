# Infrastructure Index (2025-11-14)

This index keeps `/opt` organized by describing what lives where, which services are active, and where to find their secrets, configs, and logs.

## Top-Level Layout

| Path | Purpose | Notes |
| --- | --- | --- |
| /opt/containerd | Vendor binaries that ship with the host image. | Leave untouched; only used by upstream packages. |
| /opt/dbm_docker | Primary Docker infrastructure root (apps, configs, logs, secrets, volumes). | Owned by sysadmin; secrets stay 700/600. |
| /opt/dbm_PE | Playbooks, scripts, and long-form logs for deployments. | Success + preflight logs land under `/opt/dbm_PE/logs`. |
| /opt/logs | Snapshot copy of the running success log (`dbm_success_<date>.log`). | Mirror into this repo after every task. |

## `/opt/dbm_docker/apps`

| App Dir | Compose File | External Access | State |
| --- | --- | --- | --- |
| nginx-proxy-manager | apps/nginx-proxy-manager/docker-compose.yml | Ports 80/81/443, proxy hosts `dbm.com.sa`, `n8n.dbm.com.sa`, `pxy.dbm.com.sa`, `gita.dbm.com.sa`. | Running (container `dbm_npm_ce`). |
| odoo19 | apps/odoo19/docker-compose.yml | Published behind NPM as `dbm.com.sa`. | Running (dbm_odoo19_app/db). |
| n8n | apps/n8n/docker-compose.yml | `https://n8n.dbm.com.sa` via NPM cert id=4. | Running (db, redis, app). |
| gitea | apps/gitea/docker-compose.yml | `https://gita.dbm.com.sa` (cert id=6). SSH 2222 if needed. | Running (dbm_gitea_db/app). |
| dockge | apps/dockge/docker-compose.yml | `https://dkg.dbm.com.sa` (compose-centric Docker UI). | Running (container `dbm_dockge`). |
| portainer | apps/portainer/docker-compose.yml | `https://dp.dbm.com.sa` (full-featured Docker UI). | Running (container `dbm_portainer_ce`). |
| paperless-ngx | apps/paperless-ngx/docker-compose.yml | `https://pngx.dbm.com.sa` (document management). | Running (dbm_paperless_app/db/redis). |
| odoo17 | (empty placeholder) | N/A | Structure reserved for future deployment. |
| odoo18 | (empty placeholder) | N/A | Structure reserved for future deployment. |
| grafana, pgadmin, postgresql, redis | (empty placeholders) | N/A | Create compose files here when services are scheduled. |

## Configs, Secrets, Logs, and Volumes

| Area | Path | Contents |
| --- | --- | --- |
| Configs | `/opt/dbm_docker/configs/<app>` | Runtime configs that get bind-mounted (e.g., `configs/gitea`, `configs/nginx-proxy-manager`). |
| Secrets | `/opt/dbm_docker/secrets/<app>` | Env files and credentials. Example: `secrets/gitea/gitea.env`, `secrets/gitea/admin_credentials.txt`. |
| Logs | `/opt/dbm_docker/logs/<app>` | Container logs routed via bind mounts. Example: `logs/n8n`, `logs/gitea`. |
| Volumes | `/opt/dbm_docker/volumes/<app>` | Persistent data directories for DBs and apps. |

## Service Cheat Sheet

| Service | Compose Name(s) | Secrets File(s) | Key Volumes | Notes |
| --- | --- | --- | --- | --- |
| Nginx Proxy Manager | `dbm_npm_ce` | `secrets/nginx-proxy-manager/.env` | `volumes/nginx-proxy-manager/{data,letsencrypt}` | Use API token for host + cert automation (`sysadmin@dbm.com.sa`). |
| Odoo 19 | `dbm_odoo19_app`, `dbm_odoo19_db`, `dbm_odoo19_nginx` | `secrets/odoo19/odoo.conf`, `.env` | `volumes/odoo19/{addons,data,sessions}`, `configs/odoo19` | ZATCA patch stored under `apps/odoo19/patches`. |
| n8n | `dbm_n8n_app`, `dbm_n8n_db`, `dbm_n8n_redis` | `secrets/n8n/n8n.env` | `volumes/n8n/{data,postgres,redis}` | Health endpoint `/healthz` wired into Compose. |
| Gitea | `dbm_gitea_app`, `dbm_gitea_db` | `secrets/gitea/gitea.env`, `secrets/gitea/admin_credentials.txt` | `volumes/gitea/{data,postgres}`, `configs/gitea`, `logs/gitea` | Admin `dbmadmin` created; credentials stored offline only. |
| Dockge | `dbm_dockge` | N/A | `volumes/dockge/data` | Compose-centric UI at `https://dkg.dbm.com.sa`, stacks root `/opt/dbm_docker/apps`. |
| Portainer | `dbm_portainer_ce` | N/A | `volumes/portainer/data` | Full Docker management at `https://dp.dbm.com.sa`, fresh install. |
| Paperless-ngx | `dbm_paperless_app`, `dbm_paperless_db`, `dbm_paperless_redis` | `secrets/paperless-ngx/paperless.env` | `volumes/paperless-ngx/{appdata,media,consume,pgdata}` | Document management at `https://pngx.dbm.com.sa`. |

## Logging & Index Maintenance

1. Append every finished task to `/opt/logs/dbm_success_<date>.log`.
2. Mirror those entries into `docs/success-log.md` inside this repo (keep UTC + AST stamps).
3. Update this index whenever a directory, compose file, or domain changes so `/opt` stays discoverable.
4. Commit + push the repo to both on-prem Gitea (`gita.dbm.com.sa/dbm/dbm_docker_server`) and GitHub once remotes are configured.
