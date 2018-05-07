<p align="center" style="font-size: 10rem; margin-bottom: 0;">📟</p>

# GraphQL CLI

**Handle your GraphQL development workflow with a single command line tool.**

[![](../assets/view-on-github.svg)](https://github.com/graphql-cli/graphql-cli)

The GraphQL CLI is a multi-purpose command line tool for common GraphQL development workflows. A Swiss Army Knife for GraphQL development.

## Features

👩‍💻 Code generation

📐 Schema handling

👢 Bootstrapping

🎊 Project configuration

➕ And more

## Install

Install with `npm`:

```sh
npm install -g graphql-cli
```

Install with `yarn`:

```sh
yarn global add graphql-cli
```

## Usage

The `graphql-cli` can be used via `graphql` or the shorter `gql` command.

## Command overview

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
