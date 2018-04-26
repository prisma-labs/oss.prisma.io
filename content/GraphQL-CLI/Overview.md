# GraphQL CLI

[**Open on GitHub**](https://github.com/graphql-cli/graphql-cli)

## Description

The GraphQL CLI is a multi-purpose command line tool for common GraphQL development workflows. Think of it as a "Swiss Army Knife" for GraphQL.

## Install

Install with **`npm`**:

```sh
npm install -g graphql-cli
```

Install with `**yarn**`:

```sh
yarn global add graphq-cli
```

## Usage

The `graphql-cli` can be used via `graphql` or the short `gql` commands.

### Command overview

```
Usage: graphql [command]

Commands:
  graphql init                Setup .graphqlconfig file
  graphql add-endpoint        Add new endpoint to .graphqlconfig
  graphql add-project         Add new project to .graphqlconfig
  graphql get-schema          Download schema from endpoint
  graphql schema-status       Show source & timestamp of local schema
  graphql create [directory]  Bootstrap a new GraphQL project
  graphql ping                Ping GraphQL endpoint
  graphql query <file>        Run query/mutation
  graphql diff                Show a diff between two schemas
  graphql playground          Open interactive GraphQL Playground
  graphql lint                Check schema for linting errors
  graphql prepare             Bundle schemas and generate bindings

Options:
  --dotenv       Path to .env file                                      [string]
  -p, --project  Project name                                           [string]
  -h, --help     Show help                                             [boolean]
  -v, --version  Show version number                                   [boolean]

Examples:
  graphql init                 Interactively setup .graphqlconfig file
  graphql get-schema -e dev    Update local schema to match "dev" endpoint
  graphql diff -e dev -t prod  Show schema diff between "dev" and "prod" endpoints
```

### Using with GraphQL Config

Most commands of the GraphQL CLI require the presence of a `.graphqlconfig` file in the current directory. Learn more about using the GraphQL CLI for managing your `.graphqlconfig` in the [Managing .graphqlconfig](#managing-.graphqlconfig) section.

#### Referencing a project with `--project`

The `--project` option which can be provided to most commands refers to a specific _project_ in defined in `.graphqlconfig`.

Here is an example of what a `.graphqlconfig` with two projects might look like:

```yml
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

**Example 1*: Download the GraphQL schema of the the `database` project and store it in `src/generated/prisma.graphql`

```sh
graphql get-schema --project database
```

**Example 2**: Add a new endpoint to the `app` project

```sh
graphql add-endpoint --project app
```

#### Environment variables

The GraphQL CLI supports usage of `.env` files for setting environment variables.

## Workflows

### Bootstrapping GraphQL boilerplates

### Schema handling

### Code generation

### Managing .graphqlconfig

### Misc
