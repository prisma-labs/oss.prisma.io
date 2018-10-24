# Overview

<a href="https://github.com/prisma/graphqlgen"><img src="https://imgur.com/fTa1vMv.png" alt="Prisma" height="36px"></a>

> Generate & scaffold type-safe resolvers based on your GraphQL Schema in TypeScript, Flow & Reason

## Motivation

Programming in type-safe environments provides a lot of benefits and gives you confidence about your code. `graphqlgen` leverages the strongly typed GraphQL schema with the goal of making your backend type-safe while reducing the need to write boilerplate through code generation.

#### Supported languages

- `TypeScript`
- `Flow` ([coming soon](https://github.com/prisma/graphqlgen/issues/130))
- `Reason` ([coming soon](https://github.com/prisma/graphqlgen/issues/130))

## Install

You can install the `graphqlgen` CLI with the following command:

```bash
npm install -g graphqlgen
```

## Usage

<Details><Summary><b>Note: Using <code>graphqlgen</code> in production</b></Summary>
<br />

While `graphqlgen` is ready to be used in production, it's still in active development and there might be breaking changes before it hits 1.0. Most changes will just affect the configuration and generated code layout but not the behaviour of the code itself.

</Details>

---

Once installed, you can invoke the CLI as follows:

```
graphqlgen
```

The invocation of the command depends on a configuration file called `graphqlgen.yml` which **must be located in the directory where `graphqlgen` is invoked**. Here is an example:

```yml
language: typescript

schema: ./src/schema.graphql
context: ./src/types.ts:Context
models:
  files:
    - ./src/generated/prisma-client/index.ts

output: ./src/generated/graphqlgen.ts

resolver-scaffolding:
  output: ./src/generated/tmp-resolvers/
  layout: file-per-type
```