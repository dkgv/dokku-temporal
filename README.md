# Dokku Temporal Plugin

A Dokku plugin for managing Temporal workflow platform services with existing Dokku database plugins.

## Prerequisites

Before installing this plugin, ensure you have the appropriate database plugin installed:

```bash
# For PostgreSQL
dokku plugin:install https://github.com/dokku/dokku-postgres.git postgres

# For MySQL
dokku plugin:install https://github.com/dokku/dokku-mysql.git mysql
```

## Installation

```bash
dokku plugin:install https://github.com/your-repo/dokku-temporal.git temporal
```

## Configuration

The Temporal service can be configured using a YAML configuration file that defines the service's components and their settings. The configuration file is optional - if not provided, the service will use default settings.

### Default Configuration

When no configuration file is provided, the service will use the following default settings:
- Core Temporal server with gRPC ports (7233 and 7234)
- Optional Web UI (port 8080)
- Optional metrics stack (Prometheus and Grafana)
- PostgreSQL database (managed by Dokku's postgres plugin)
- Elasticsearch for visibility

### Custom Configuration

To customize the Temporal service, create a YAML configuration file with your desired settings. The configuration file should follow the structure defined in [temporal-config.yaml](temporal-config.yaml).

```bash
# Create a Temporal service with custom configuration
dokku temporal:create my-temporal --config /path/to/config.yaml
```

### Available Configuration Options

The configuration file can specify:
- Core Temporal server settings
- Database connection settings (automatically managed by Dokku's database plugin)
- Elasticsearch configuration
- Optional UI settings
- Optional metrics stack (Prometheus and Grafana)
- Network settings
- Volume configurations
- Security settings

### Example Usage

```bash
# Create a Temporal service with default configuration
dokku temporal:create my-temporal

# Create with custom configuration
dokku temporal:create my-temporal --config /path/to/config.yaml

# View current configuration
dokku temporal:config my-temporal

# Get specific configuration value
dokku temporal:config:get my-temporal server.image

# Get UI URL
dokku temporal:config:web-ui my-temporal

# Get Prometheus URL
dokku temporal:config:prometheus my-temporal

# Get Grafana URL
dokku temporal:config:grafana my-temporal
```

### DNS Configuration

The Temporal services use direct port mapping and require proper DNS configuration. You'll need to:

1. Add an A/AAAA record pointing to your Dokku host's IP address
2. Ensure the ports are accessible through your firewall:
   - Web UI: 8080
   - Prometheus: 9090
   - Grafana: 8085

For example, if your Dokku host is `example.com`, you'll need to:

1. Create an A/AAAA record:
   - Host: `my-temporal`
   - Points to: Dokku host's IP address

2. Access the services at:
   - Web UI: `http://my-temporal.example.com:8080`
   - Prometheus: `http://my-temporal.example.com:9090`
   - Grafana: `http://my-temporal.example.com:8085`

Note: Since this is a plugin and not a Dokku app, you cannot use Dokku's domains plugin to manage custom domains. You'll need to manage DNS records directly through your DNS provider.

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
# Start the service
dokku temporal:start my-temporal

# Stop the service
dokku temporal:stop my-temporal

# Restart the service
dokku temporal:restart my-temporal
```

### Link to Apps

```bash
# Link Temporal service to an app
dokku temporal:link my-temporal my-app

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
# View service logs (both server and database)
dokku temporal:logs my-temporal

# Enter the service container
dokku temporal:enter my-temporal

# Connect to Temporal CLI
dokku temporal:connect my-temporal
```

### Clean Up

```bash
# Destroy a service (use --force if linked to apps)
dokku temporal:destroy my-temporal
```

## Available Commands

```
create <service> [db-type]     Create a new Temporal service with optional database type (postgres/mysql/cassandra)
start <service>               Start Temporal service
stop <service>                Stop Temporal service
restart <service>             Restart Temporal service
info <service>                Show Temporal service information
list                          List all Temporal services
link <service> <app>          Link Temporal service to an app
unlink <service> <app>        Unlink Temporal service from an app
exists <service>              Check if Temporal service exists
logs <service> [tail-num]     Show Temporal service logs
enter <service>               Enter Temporal service container
connect <service>             Connect to Temporal service
destroy <service> [--force]   Destroy Temporal service
```

## Notes

- When creating a service, ensure the specified database type is supported
- The Temporal server runs on port 7233 by default
- Use the `--force` flag with caution when destroying services linked to apps
