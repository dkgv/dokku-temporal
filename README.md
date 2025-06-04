# dokku-temporal

A Dokku plugin for managing Temporal workflow platform services with existing Dokku database plugins. This plugin automatically handles database connectivity, schema setup, and provides both the Temporal server and Web UI.

## Prerequisites

Before installing this plugin, ensure you have the appropriate database plugin installed:

```bash
# For PostgreSQL (recommended)
dokku plugin:install https://github.com/dokku/dokku-postgres.git postgres

# For MySQL (should also be supported)
dokku plugin:install https://github.com/dokku/dokku-mysql.git mysql
```

## Installation

```bash
dokku plugin:install https://github.com/dkgv/dokku-temporal.git temporal
```

## Quick Start

```bash
# Create a Temporal service with PostgreSQL (default)
dokku temporal:create my-temporal

# Start the service (includes server + Web UI)
dokku temporal:start my-temporal

# Access the Web UI
open http://localhost:8080

# Link to your Dokku app
dokku temporal:link my-temporal my-app
```

## Usage

### Create a Temporal Service

```bash
# Create a new Temporal service with PostgreSQL (default)
dokku temporal:create my-temporal

# Create with MySQL
dokku temporal:create my-temporal mysql

# Note: Only PostgreSQL and MySQL are supported as they must be managed by existing Dokku plugins
```

### Manage Service Lifecycle

```bash
# Start the service (starts both Temporal server and Web UI)
dokku temporal:start my-temporal

# Stop the service
dokku temporal:stop my-temporal

# Restart the service
dokku temporal:restart my-temporal
```

### Access Points

After starting a service, you can access:

- **Temporal Server (gRPC)**: `localhost:7233` - For SDK connections
- **Temporal Web UI**: `http://localhost:8080` - For browser-based monitoring and management

### Link to Apps

```bash
# Link Temporal service to an app
dokku temporal:link my-temporal my-app

# This sets environment variables on your app:
# TEMPORAL_ADDRESS=<server-ip>:7233
# TEMPORAL_NAMESPACE=default

# Unlink Temporal service from an app
dokku temporal:unlink my-temporal my-app
```

### View Service Information

```bash
# Show service status
dokku temporal:info my-temporal

# List all Temporal services
dokku temporal:list

# Check if service exists
dokku temporal:exists my-temporal
```

### Access Logs and CLI

```bash
# View Temporal server logs
dokku temporal:logs my-temporal

# View both Temporal and PostgreSQL logs
dokku temporal:logs my-temporal --db

# Enter the Temporal server container
dokku temporal:enter my-temporal

# Connect to Temporal CLI (for advanced operations)
dokku temporal:connect my-temporal
```

### Clean Up

```bash
# Destroy a service (use --force if linked to apps)
dokku temporal:destroy my-temporal

# Force destroy (removes links automatically)
dokku temporal:destroy my-temporal --force
```

## Available Commands

```
create <service> [db-type]     Create a new Temporal service with optional database type (postgres/mysql)
start <service>               Start Temporal service (includes server + Web UI)
stop <service>                Stop Temporal service
restart <service>             Restart Temporal service
info <service>                Show Temporal service information
list                          List all Temporal services
link <service> <app>          Link Temporal service to an app
unlink <service> <app>        Unlink Temporal service from an app
exists <service>              Check if Temporal service exists
logs <service> [tail-num]     Show Temporal service logs (add --db for database logs)
enter <service>               Enter Temporal service container
connect <service>             Connect to Temporal service CLI
destroy <service> [--force]   Destroy Temporal service
```

## Networking and Connectivity

The plugin automatically handles complex networking scenarios:

- **Database Connectivity**: Automatically detects and uses the correct IP addresses and networks for database connections
- **Bridge Network Support**: Works reliably on Docker's default bridge network
- **Custom Network Detection**: Prefers Dokku's custom networks when available
- **IP-based Resolution**: Uses IP addresses instead of hostnames to avoid DNS resolution issues

## Configuration

### Default Configuration

When no configuration file is provided, the service uses these defaults:
- **Temporal Server**: Core server with gRPC ports (7233, 7234, 7235)
- **Web UI**: Available on port 8080
- **Database**: PostgreSQL (managed by Dokku's postgres plugin)
- **Namespace**: `default` namespace created automatically
- **Schema Setup**: Automatic schema initialization

### Advanced Configuration

The plugin includes support for custom configuration files (see `temporal-config.yaml` for reference), but most users won't need custom configuration for basic usage.

### Environment Variables Set on Linked Apps

When you link a Temporal service to an app, these environment variables are automatically set:

```bash
TEMPORAL_ADDRESS=<temporal-server-ip>:7233
TEMPORAL_NAMESPACE=default
```

Your application can use these to connect to the Temporal service.

## Troubleshooting

### Service Won't Start

```bash
# Check if the database service exists and is running
dokku postgres:info my-temporal

# Check Temporal logs for detailed error messages
dokku temporal:logs my-temporal

# Check database connectivity
dokku temporal:logs my-temporal --db
```

### Web UI Shows "Internal Error"

The UI automatically connects to the Temporal server using IP addresses. If you see connection errors:

```bash
# Restart the service to refresh IP addresses
dokku temporal:restart my-temporal

# Check server logs
dokku temporal:logs my-temporal
```

### Database Connection Issues

The plugin automatically handles database connectivity, including:
- Starting stopped database containers
- Using correct IP addresses for connection
- Setting proper environment variables for Temporal

If you encounter database issues, check that the underlying database service is healthy:

```bash
dokku postgres:info my-temporal
```

## Architecture

The plugin creates and manages:

1. **Database Service**: PostgreSQL or MySQL container (via Dokku database plugins)
2. **Temporal Server**: Main Temporal server container with auto-setup
3. **Web UI**: Temporal Web UI container for browser access
4. **Networking**: Proper container networking and IP address resolution

All components run on the same Docker network and communicate via internal IP addresses for maximum reliability.

## Production Notes

- The plugin uses `temporalio/auto-setup` image which includes automatic schema setup
- For production deployments, consider the security implications of exposed ports
- The default namespace has a 24-hour retention period
- Database services are managed independently and can be backed up using standard Dokku database plugin commands

## DNS and External Access

The Temporal services use direct port mapping and require proper DNS configuration for external access:

### Required Ports
- **Temporal Server (gRPC)**: 7233 (primary), 7234 (admin), 7235 (additional)
- **Web UI**: 8080

### External DNS Setup

For external access beyond localhost, you'll need to:

1. **Add DNS records** pointing to your Dokku host's IP address
2. **Configure firewall** to allow access to the required ports
3. **Update CORS settings** if accessing Web UI from different domains

Example for external access:
```bash
# If your Dokku host is example.com with IP 1.2.3.4
# Create DNS record: my-temporal.example.com → 1.2.3.4

# Access via:
# - Server: my-temporal.example.com:7233
# - Web UI: http://my-temporal.example.com:8080
```

**Note**: Since this is a plugin and not a Dokku app, you cannot use Dokku's domains plugin to manage custom domains. You'll need to manage DNS records directly through your DNS provider.

## Examples

```bash
# Create Temporal service for production app
dokku temporal:create prod-temporal postgres
dokku temporal:start prod-temporal

# Link to your production app
dokku temporal:link prod-temporal my-production-app

# Your app now has these environment variables:
# TEMPORAL_ADDRESS=<ip>:7233
# TEMPORAL_NAMESPACE=default

# Deploy your app
dokku apps:restart my-production-app
```