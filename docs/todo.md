# Task Tracker (2025-11-14)

Status board for outstanding work on the `/opt/dbm_docker` host. Update this list whenever we add a new service, rotate credentials, or close out an item.

## High Priority

1. **Portainer exposure**  
   - Bring up `apps/portainer/docker-compose.yml` and attach to `dbm_reverse_proxy`.  
   - Create NPM proxy host `dp.dbm.com.sa` with Let’s Encrypt cert and baseline auth controls.  
   - Log the deployment (success log + index update) once HTTPS is verified.
2. **Multi-repo publishing plan**  
   - Confirm which additional projects (Odoo custom modules, automation scripts, etc.) need mirrors on Gitea + GitHub.  
   - Define naming conventions, remotes, and credential storage for each.  
   - Capture the workflow in `docs/deployment_playbook.md` when finalized.
3. **Automation for success-log sync**  
   - Script the copy from `/opt/logs/dbm_success_<date>.log` into `docs/success-log.md` to prevent drift.  
   - Wire script into a cron or reusable command under `/opt/dbm_PE/scripts/`.

## Nice to Have

- Document preflight checklist outputs in `docs/` whenever the host baseline changes (kernel updates, Docker upgrades).  
- Add service-specific runbooks (n8n, Gitea, Odoo) with backup/restore instructions.  
- Mirror this `docs/todo.md` summary into any future knowledge base (Obsidian, Confluence) for broader visibility.

## Recently Completed (Nov 14, 2025)

- ✅ Created `docs/index.md` to map every `/opt` path, service, and secret.  
- ✅ Pushed `dbm_docker_server` to `gita.dbm.com.sa/dbmadmin/dbm_docker_server` and `github.com/digitalbuildmasters/dbm_docker_server`.  
- ✅ Logged all successes (Odoo, n8n, NPM, Gitea, documentation) with UTC + AST timestamps.
