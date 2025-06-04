# Dokku Temporal Plugin

A Dokku plugin for managing Temporal workflow platform services. Automatically handles database connectivity, schema setup, and provides both Temporal server and Web UI.

## Prerequisites

```bash
# Install database plugin (choose one)
dokku plugin:install https://github.com/dokku/dokku-postgres.git postgres
dokku plugin:install https://github.com/dokku/dokku-mysql.git mysql
```

## Installation

```bash
dokku plugin:install https://github.com/dkgv/dokku-temporal.git temporal
```

## Quick Start

```bash
# Create and start service
dokku temporal:create my-temporal
dokku temporal:start my-temporal

# Access points:
# - Server (gRPC): localhost:7233
# - Web UI: http://localhost:8080 (local) or SSH tunnel (remote)

# Link to app
dokku temporal:link my-temporal my-app
```

## Commands

```
create <service> [db-type]     Create Temporal service (postgres/mysql)
start <service>               Start service (server + Web UI)
stop <service>                Stop service
restart <service>             Restart service
info <service>                Show service information
list                          List all services
link <service> <app>          Link service to app (sets TEMPORAL_ADDRESS, TEMPORAL_NAMESPACE)
unlink <service> <app>        Unlink service from app
logs <service> [--db]         Show logs (add --db for database logs)
destroy <service> [--force]   Destroy service
```

## Security

The Web UI is **localhost-only** for security. 

**Local development:** Direct access at `http://localhost:8080`

**Remote server (VPS):** Use SSH tunnel:
```bash
ssh -L 8080:localhost:8080 user@your-server.com
# Then browse to http://localhost:8080
```

**Why this approach:**
- Zero configuration (no passwords/certificates)
- Maximum security (not accessible from internet)
- Works with any Dokku deployment
- Industry standard for admin interfaces

## External Access

**Temporal Server (for apps):** Can be accessed externally on port 7233
```bash
# Set up DNS: my-temporal.example.com → your-server-ip
# Apps connect to: my-temporal.example.com:7233
```

**Web UI:** Localhost-only (use SSH tunnel)

## Troubleshooting

**Service won't start:**
```bash
dokku postgres:info my-temporal  # Check database
dokku temporal:logs my-temporal  # Check Temporal logs
```

**UI shows "Internal Error":**
```bash
# For remote access, ensure SSH tunnel is active
ssh -L 8080:localhost:8080 user@your-server.com

# Then restart if needed
dokku temporal:restart my-temporal
```

**Database issues:** Plugin auto-handles connectivity, IP resolution, and container startup.

## Examples

```bash
dokku temporal:create prod-temporal
dokku temporal:start prod-temporal
dokku temporal:link prod-temporal my-app
dokku apps:restart my-app

# Monitor via SSH tunnel:
ssh -L 8080:localhost:8080 deploy@prod-server.com
```

## Architecture

- **Database:** PostgreSQL/MySQL container (via Dokku plugins)
- **Server:** Temporal server with auto-setup (`temporalio/auto-setup`)
- **UI:** Web interface (`temporalio/ui`)
- **Networking:** Automatic IP-based connectivity (works on bridge network)

Environment variables set on linked apps:
```
TEMPORAL_ADDRESS=<server-ip>:7233
TEMPORAL_NAMESPACE=default
```