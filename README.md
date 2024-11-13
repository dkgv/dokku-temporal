# dokku-temporal

Plugin for managing Temporal servers in Dokku environments.

## Commands

```
temporal:create <app>              Create a new Temporal server for an app
temporal:destroy <app>             Destroy the Temporal server for an app
temporal:start <app>               Start the Temporal server
temporal:stop <app>                Stop the Temporal server
temporal:info <app>                Display information about the Temporal server
temporal:list                      List all Temporal servers
temporal:set-db-type <app> <type>  Set database type (postgres, mysql, cassandra)
temporal:set-db-url <app> <url>    Set database URL
temporal:get-db-config <app>       Show database configuration
```

## Configuration

### Setting up the database

```bash
# For PostgreSQL
dokku temporal:set-db-type myapp postgres
dokku temporal:set-db-url myapp "user:password@localhost:5432/temporal"

# For MySQL
dokku temporal:set-db-type myapp mysql
dokku temporal:set-db-url myapp "user:password@localhost:3306/temporal"

# For Cassandra
dokku temporal:set-db-type myapp cassandra
dokku temporal:set-db-url myapp "user:password@localhost:9042"
```

## Usage Example

```bash
# Create a new app
dokku apps:create myapp

# Configure database
dokku temporal:set-db-type myapp postgres
dokku temporal:set-db-url myapp "user:password@localhost:5432/temporal"

# Create and start Temporal server
dokku temporal:create myapp

# Check status
dokku temporal:info myapp
```
