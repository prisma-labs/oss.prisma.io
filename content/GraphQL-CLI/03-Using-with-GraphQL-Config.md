# Using with GraphQL Config

Most commands of the GraphQL CLI require the presence of a `.graphqlconfig` file in the current directory.

> Learn more about using the GraphQL CLI for _managing_ your `.graphqlconfig` in the [**Managing .graphqlconfig**](#managing-graphqlconfig) section.

## Referencing a project with `--project`

The `--project` option which can be provided to most commands refers to a specific _project_ defined in `.graphqlconfig`.

Here is an example of what a `.graphqlconfig` with two projects might look like:

```yaml
projects:
  app:
    schemaPath: src/schema.graphql
    extensions:
      endpoints:
        default: http://localhost:4000
  database:
    schemaPath: src/generated/prisma.graphql
    extensions:
      prisma: database/prisma.yml
```

When using a command for a specific project, the project needs to be referred to by its _key_ in the `projects` map.

**Example 1**: Download the GraphQL schema of the the `database` project and store it in `src/generated/prisma.graphql`

```sh
graphql get-schema --project database
```

**Example 2**: Add a new endpoint to the `app` project

```sh
graphql add-endpoint --project app
```

## Environment variables

Instead of hardcoding value in `.graphqlconfig`, you can use environment variables. To learn more about the required syntax, check out the [corresponding section](../GraphQL-Config/Overview.md#environment-variables) in the GraphQL Config docs.

### Usage of `.env` files

The GraphQL CLI supports usage of [`.env`](https://github.com/motdotla/dotenv) files for setting environment variables.

As an example, consider the following `.graphqlconfig`:

```yaml
projects:
  app:
    schemaPath: src/schema.graphql
    extensions:
      endpoints:
        default: ${env:API_ENDPOINT}
  database:
    schemaPath: src/generated/${env:PRISMA_SCHEMA_FILENAME}.graphql
    extensions:
      prisma: database/prisma.yml
```

This file references two environment variables called: `API_ENDPOINT` and `PRISMA_SCHEMA_FILENAME`. You can set those either by using your shell or by providing a `.env` file which will be automatically considered by the GraphQL CLI when you're executing a command.

Assume the directory where the `.graphqlconfig` is located also contains the following `.env` file:

```
API_ENDPOINT="http://localhost:4000"
PRISMA_SCHEMA_FILENAME="prisma"
```

Now, when executing a CLI command, the values for the variables specified in `.graphqlconfig` will be automatically infered by the CLI based on the values in `.env`.

**Example 1**: Ping the endpoint of the `app` project

```sh
graphql ping --project app
```

Because `API_ENDPOINT` is set to `http://localhost:4000`, the GraphQL CLI will ping that URL when invoking the command.

**Example 2**: Download the GraphQL schema of the `database` project and store it in the specified path

```sh
graphql get-schema --project database
```

### Usage of multiple `.env` files

Environment variables are often used to differentiate between multiple _development environments_. In those cases, you might want to specify multiple `.env` files that each represent a different environment, e.g. `dev` and `prod`. You can simply append the environment name to the file name, e.g. `.env.dev` or `.env.prod`.

The GraphQL CLI takes a special option called `--dotenv`, allowing you to point to a specific `.env` file and thus easily switch between different environments when using the CLI.

Assume the directory where the `.graphqlconfig` is located also contains the following two `.env` files:

`.env.dev`

```
API_ENDPOINT="http://localhost:4000"
PRISMA_SCHEMA_FILENAME="prisma"
```

`.env.prod`

````
API_ENDPOINT="http://myapiserver.com/graphql"
PRISMA_SCHEMA_FILENAME="prisma"
````

When invoking CLI commands, you can now refer either of these files using the `--dotenv` option:

```
graphql ping --project app --dotenv .env.dev
# or
graphql ping --project app --dotenv .env.prod
```
