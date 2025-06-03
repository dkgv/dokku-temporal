# Temporal Plugin Configuration

## Service Configuration

When creating a Temporal service, you can specify the following configuration:

```bash
dokku temporal:create <service> [db-type]
```

Where:
- `db-type`: Optional database type (postgres/mysql)

## Application Configuration

When linking a Temporal service to an application, the following environment variables are automatically set:

- `TEMPORAL_SERVICE_NAME`: Name of the linked Temporal service
- `TEMPORAL_DB_TYPE`: Type of database used by the Temporal service

## Advanced Configuration

You can configure additional Temporal settings by setting these environment variables on your application:

- `TEMPORAL_NAMESPACE`: Custom namespace for Temporal workflows (default: default)
- `TEMPORAL_WORKFLOW_TIMEOUT`: Default workflow timeout in seconds
- `TEMPORAL_ACTIVITY_TIMEOUT`: Default activity timeout in seconds
- `TEMPORAL_TASK_QUEUE`: Custom task queue name
- `TEMPORAL_WORKFLOW_ID_PREFIX`: Prefix for workflow IDs

Example:
```bash
dokku config:set myapp TEMPORAL_NAMESPACE=my-namespace
```
