<p align="center" style="font-size: 10rem; margin-bottom: 0;">🗂</p>

# GraphQL Import

**Easily compose GraphQL Schemas**

You may want to split a schema definition into multiple files in large applications, `graphql-import` is a package that allows importing &amp; exporting schema definitions in GraphQL SDL (also refered to as GraphQL modules).

## Features

🍺 Import specific types: `# import A from 'schema.graphql'`

🍻 Import multiple types: `# import A, B, C from 'schema.graphql'`

✳️ Import all types: `# import * from 'schema.graphql'`

🌲 Import root fields: `# import Query.* from 'schema.graphql'`

🛤 Relative paths: `# import Post from "../database/schema.graphql"`

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

## Example

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

type App {
  # test 1
  posts: [Post]
  name: String
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

## Import from strings

`graphql-import` supports loading schemas from memory instead of by reading files. You can pass objects instead of a filenames to `importSchema`.

```js
import { importSchema } from 'graphql-import'

const commentsSchema = `
  type Comment {
    id: ID!
    text: String!
  }`

const postsSchema = `
  # import * from 'comments.graphql'

  type Post {
    comments: [Comment]
    id: ID!
    text: String!
    tags: [String]
  }`

const appSchema = `
  # import Post from "posts"

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
