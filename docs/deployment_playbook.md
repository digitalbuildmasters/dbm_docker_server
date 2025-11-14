# Deployment Playbook (Nov 2025)

This playbook distills the successful steps captured in the success log into repeatable phases.

## 1. Base OS Hardening

1. Capture diagnostics with `uname -a`, `lsb_release -a`, `timedatectl`, `free -h`, `df -h`, `ss -ltnp ':(80|81|443)'`, `docker info`.
2. Create `sysadmin` user and add to `sudo`, `dbm-ops (9000)`, and `docker (9001)`.
3. Install `chrony`, `tree`, `tzdata-legacy`; enable `chrony` and confirm NTP sync.
4. Apply sysctl: `net.ipv4.ip_forward=1` and `net.bridge.bridge-nf-call-iptables=1`; load `br_netfilter`.
5. Install Docker CE 29.0.0, Compose v2.40.3, containerd 2.1.5; disable swap.
6. Create `/opt/dbm_docker` + `/opt/dbm_PE` hierarchies, enforce `secrets` 700/640 perms.

## 2. Odoo19 + Internal nginx

1. Run `/opt/dbm_docker/scripts/odoo19_enterprise_setup.sh` to provision `dbm_odoo19_db`, `dbm_odoo19_app`, and mount enterprise + addons volumes.
2. Add `dbm_odoo19_nginx` service to `docker-compose.yml` with upstream timeouts and websocket headers.
3. Attach `dbm_odoo19_app` and `dbm_odoo19_nginx` to shared `dbm_reverse_proxy` network.
4. Update NPM proxy host for `dbm.com.sa` / `www.dbm.com.sa` to forward to `dbm_odoo19_nginx` (port 80) with Let’s Encrypt cert `npm-2`.
5. Apply ZATCA QR fix:
   - `docker exec -u 0 dbm_odoo19_app cp /.../account_move.py /.../account_move.py.original.<timestamp>`
   - `docker cp patches/account_move_zatca_fix.py dbm_odoo19_app:/.../account_move.py`
   - `docker compose restart odoo` and confirm `timedelta(hours=3)` via `grep`.

## 3. Nginx Proxy Manager

1. Deploy `dbm_npm_ce` via `/opt/dbm_docker/apps/nginx-proxy-manager/docker-compose.yml`.
2. Store secrets in `/opt/dbm_docker/secrets/nginx-proxy-manager/.env`; rotate admin password (`sysadmin@dbm.com.sa`).
3. Use API (`POST /api/tokens`) to obtain JWT for automation tasks (host creation, cert issuance).

## 4. n8n Stack

1. Directories: `/opt/dbm_docker/apps/n8n`, `configs/n8n`, `logs/n8n`, `volumes/n8n/{data,postgres,redis}`.
2. Secret file `/opt/dbm_docker/secrets/n8n/n8n.env` contains DB creds, encryption key, and basic-auth settings.
3. Compose stack: Postgres 16, Redis 7, `n8nio/n8n:1.76.3`. Attach to `dbm_reverse_proxy`.
4. After `docker compose up -d`, fix env validation (set `EXECUTIONS_DATA_SAVE_ON_SUCCESS=none`).
5. Issue Let’s Encrypt cert for `n8n.dbm.com.sa` using NPM API: `POST /api/nginx/certificates` then update proxy host to reference new `certificate_id` and verify with `curl -Ik --resolve ...`.

## 5. NPM Admin Proxy (`pxy.dbm.com.sa`)

1. Create proxy host pointing to `dbm_npm_ce:81` with websocket + force SSL.
2. Request certificates via NPM API for `pxy.dbm.com.sa` and bind to host 3.
3. Confirm TLS handshake using `curl -Ik --resolve pxy.dbm.com.sa:443:127.0.0.1 https://pxy.dbm.com.sa`.

## 6. Gitea Stack

1. Secrets `/opt/dbm_docker/secrets/gitea/gitea.env` (DB creds, secret keys, tokens).
2. Compose stack: `dbm_gitea_db` (Postgres 16) + `dbm_gitea_app` (Gitea 1.22.3) with volumes for `/data`, `/etc/gitea`, `/var/log/gitea`.
3. Start stack: `docker compose up -d` and expose HTTP 3000 + SSH 2222 (internal).
4. Create admin via `docker exec -u 1000 dbm_gitea_app gitea admin user create --username dbmadmin ...`.
5. Protect generated credentials in `/opt/dbm_docker/secrets/gitea/admin_credentials.txt` (chmod 600).
6. Front with NPM by adding proxy host `gita.dbm.com.sa` and issuing Let’s Encrypt cert (same API flow as above).

## Verification Checklist

- `docker compose ps` for each stack shows `healthy` containers.
- `curl -Ik --resolve <domain>:443:127.0.0.1 https://<domain>` returns `HTTP/2 200`.
- Success log appended with UTC + AST timestamp.
- Re-run `tree -p -L 2 /opt/dbm_docker` to confirm directories + perms when new services added.
