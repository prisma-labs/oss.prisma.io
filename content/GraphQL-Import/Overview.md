# Overview

<a href="https://github.com/prisma/graphql-import"><img src="https://imgur.com/fTa1vMv.png" alt="Prisma" height="36px"></a>

## Description

You may want to split a schema definition into multiple files in large applications, `graphql-import` is a package that allows importing &amp; exporting schema definitions in GraphQL SDL (also referred to as GraphQL modules).

> **Note**: `graphql-import` currently uses a custom syntax based on SDL comments! The GraphQL working group currently debates including a proper import syntax into the offical GraphQL specification, more info [here](https://github.com/graphql/graphql-wg/blob/master/notes/2018-02-01.md#present-graphql-import).

## Features

- Import specific types: `# import A from 'schema.graphql'`
- Import multiple types: `# import A, B, C from 'schema.graphql'`
- Import all types: `# import * from 'schema.graphql'`
- Import root fields: `# import Query.* from 'schema.graphql'`
- Import root fields and all types: `# import Query.*, Mutation.*, * from 'schema.graphql'`
- Relative paths: `# import Post from "../database/schema.graphql"`

## Install

```sh
yarn add graphql-import
```

## Usage

`importSchema` receives the filename of your main GraphQL SDL. It will concatenate all files and produce a GraphQL SDL with the generated schema.

```ts
import { importSchema } from 'graphql-import'
import { makeExecutableSchema } from 'graphql-tools'

const typeDefs = importSchema('schema.graphql')
const resolvers = {}

const schema = makeExecutableSchema({ typeDefs, resolvers })
```

## Examples

### Import specific types

Assume the following directory structure:

```
.
├── schema.graphql
├── posts.graphql
└── comments.graphql
```

`schema.graphql`

```graphql
# import Post from "posts.graphql"

type Query {
  posts: [Post]
}
```

`posts.graphql`

```graphql
# import Comment from 'comments.graphql'

type Post {
  comments: [Comment]
  id: ID!
  text: String!
  tags: [String]
}
```

`comments.graphql`

```graphql
type Comment {
  id: ID!
  text: String!
}
```

Running `console.log(importSchema('schema.graphql'))` produces the following output:

```graphql
type Query {
  posts: [Post]
}

type Post {
  comments: [Comment]
  id: ID!
  text: String!
  tags: [String]
}

type Comment {
  id: ID!
  text: String!
}
```

### Import root fields

In the previous example you'd have a `schema.graphql` file that imports every specific type and then make a `Query` and `Mutation` type that lists all root fields. If you have many root fields, this can become hard to maintain. An alternative way is to use `Query` and `Mutation` in each file, so that your `schema.graphql` only has to import each file to create the schema.

Assume the following directory structure: 

```
.
├── schema.graphql
├── posts.graphql
└── users.graphql
```


`schema.graphql`

```graphql
# import Query.*, Mutation.* from "posts.graphql"
# import Query.*, Mutation.* from "users.graphql"
```

`posts.graphql`

```graphql
type Post {
  id: ID!
  text: String!
}

type Query {
  posts: [Post]
}

type Mutation {
  deletePost(id: ID!): Post!
}
```

`users.graphql`

```graphql
type User {
  id: ID!
  username: String!
}

type Query {
  users: [User]
}

type Mutation {
  deleteUser(id: ID!): User!
}
```

Running `console.log(importSchema('schema.graphql'))` produces the following output:

```graphql
type Query {
  posts: [Post]
  users: [User]
}

type Mutation {
  deletePost(id: ID!): Post!
  deleteUser(id: ID!): User!
}

type Post {
  id: ID!
  text: String!
}

type User {
  id: ID!
  username: String!
}
```

### Import from strings

`graphql-import` supports loading schemas from memory instead of by reading files. You can pass objects instead of a filenames to `importSchema`.

```js
import { importSchema } from 'graphql-import'

const commentsSchema = `
  type Comment {
    id: ID!
    text: String!
  }`

const postsSchema = `
  # import * from 'commentsSchema'

  type Post {
    comments: [Comment]
    id: ID!
    text: String!
    tags: [String]
  }`

const appSchema = `
  # import Post from "postsSchema"

  type App {
    # test 1
    posts: [Post]
    name: String
  }`

const schemas = {
  appSchema, postsSchema, commentsSchema
}

console.log(importSchema(appSchema, schemas))
```

The previous script produces the following output:

```graphql
type App {
  posts: [Post]
  name: String
}

type Post {
  comments: [Comment]
  id: ID!
  text: String!
  tags: [String]
}

type Comment {
  id: ID!
  text: String!
}
```
