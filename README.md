# Dynoraptor

A command-line tool for managing DynamoDB migrations. Built on [Umzug](https://github.com/sequelize/umzug), Dynoraptor tracks executed migrations in DynamoDB and provides commands to apply, inspect and roll back changes.

## Features

- **Migration tracking:** Stores migration history in a DynamoDB table.
- **Migration lifecycle:** Apply pending migrations, inspect their status and roll back changes.
- **Umzug compatibility:** Uses the familiar Umzug migration schema.
- **Flexible configuration:** Configure migrations and AWS settings through CLI options or environment variables.
- **TypeScript support:** Generate TypeScript migration files.

## Installation

Install Dynoraptor as a development dependency:

```bash
npm install --save-dev @produce8/dynoraptor-cli
```

Run the CLI with `npx`:

```bash
npx dynoraptor --help
```

## Configuration

Dynoraptor can be configured through command-line options or environment variables in a `.env` file.

Example `.env`:

```dotenv
MIGRATIONS_PATH=migrations
AWS_ACCESS_KEY=YOUR_AWS_ACCESS_KEY
AWS_SECRET_KEY=YOUR_AWS_SECRET_KEY
AWS_REGION=us-west-2
DYNAMO_ENDPOINT_URL=
MIGRATION_TABLE_NAME=migrations
MIGRATION_PRIMARY_KEY=name
TYPESCRIPT=false
```

| Variable                | Description                                              |
| ----------------------- | -------------------------------------------------------- |
| `MIGRATIONS_PATH`       | Directory containing migration files                     |
| `AWS_ACCESS_KEY`        | AWS access key                                           |
| `AWS_SECRET_KEY`        | AWS secret key                                           |
| `AWS_REGION`            | AWS region                                               |
| `DYNAMO_ENDPOINT_URL`   | Optional DynamoDB endpoint, useful for local development |
| `MIGRATION_TABLE_NAME`  | DynamoDB table used to store migration history           |
| `MIGRATION_PRIMARY_KEY` | Partition key attribute for migration records            |
| `TYPESCRIPT`            | Generate TypeScript migration files when enabled         |

Dynoraptor expects the migration tracking table to exist before running migrations. Create the table in DynamoDB with a partition key matching `MIGRATION_PRIMARY_KEY`. The default table name is `migrations`, and the default partition key is `name`.

AWS credentials and permissions must allow the CLI to access the configured DynamoDB table.

## Usage

### Generate a migration

Create a migration file with a descriptive name:

```bash
npx dynoraptor migration:generate --name add-user-index
```

The `migration:create` command is also available as an alias.

### Run pending migrations

```bash
npx dynoraptor migrate
```

### Check migration status

```bash
npx dynoraptor migrate:status
```

### Roll back a migration

```bash
npx dynoraptor migrate:undo
```

### Roll back all migrations

```bash
npx dynoraptor migrate:undo:all
```

## Writing migrations

Dynoraptor uses the [Umzug migration schema](https://github.com/sequelize/umzug). Each migration exports an object with `up` and `down` functions.

```javascript
module.exports = {
  up: function (dynamodb, done) {
    // Apply the migration.
    done();
  },

  down: function (dynamodb) {
    return new Promise(function (resolve, reject) {
      // Revert the migration.
      resolve();
    });
  },
};
```

The `dynamodb` argument provides access to the DynamoDB client used by the migration.

Migration functions can use either a callback or return a Promise to handle asynchronous work. When using the callback, call it after the operation completes. Passing an error to the callback signals a failed migration.

Set the `TYPESCRIPT` configuration option to generate TypeScript migration files instead.

## Documentation

- [CLI options and migration schema](docs/README.md)
- [Frequently asked questions](docs/FAQ.md)

## License

[MIT](LICENSE)
