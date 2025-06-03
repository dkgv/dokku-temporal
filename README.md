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

## Testing

The plugin includes a Docker-based test environment that sets up a complete Dokku installation with all required plugins.

To run the tests:

```bash
# Build and run the test container from the root directory
cd /path/to/dokku-temporal
docker build -t dokku-temporal-test -f tests/Dockerfile .
docker run --rm dokku-temporal-test
```

The test suite will:
1. Set up a fresh Dokku installation
2. Install required plugins (postgres, mysql)
3. Run through all plugin functionality
4. Clean up after itself

You can also run the tests interactively for debugging:
```bash
docker run -it --rm dokku-temporal-test /bin/bash
```

## Configuration

See [config.md](config.md) for detailed configuration options and environment variables.

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
