# API

## Request

Sends a query to a GraphQL endpoint.

**Example**

```js
import request from "graphql-request";

const endpoint = 'https://api.graph.cool/simple/v1/movies';

const query = `query getMovie($title: String!) {
  Movie(title: $title) {
    releaseDate
    actors {
      name
    }
  }
}`

const variables = {
  title: 'Inception',
}

request(endpoint, query, variables).then(data => console.log(data));
/* ->
{ Movie:
   { releaseDate: '2010-08-28T20:00:00.000Z',
     actors: [ [Object], [Object], [Object], [Object], [Object] ] } }
*/
```

**Arguments**

* `url` (required): HTTP URL of a GraphQL server
* `query` (required): GraphQL query string
* `variables`: Object with variables

**Response**

* Promise with query response data

## GraphQL Client

Client instance representing GraphQL Client.

**Example**

```js
import { GraphQLClient } from "graphql-request";

const endpoint = 'https://api.graph.cool/simple/v1/movies';

const query = `query getMovie($title: String!) {
  Movie(title: $title) {
    releaseDate
    actors {
      name
    }
  }
}`

const variables = {
  title: 'Inception',
}

const client = new GraphQLClient(endpoint, { headers: {} });

client.request(query, variables).then(data => console.log(data));
```

### Constructor

**Arguments**

* `url` (required): HTTP URL of a GraphQL server
* `options`:
  * `method`: HTTP method
  * `headers`: HTTP headers
  * `mode`: `"navigate" | "same-origin" | "no-cors" | "cors"`
  * `credentials`: `"omit" | "same-origin" | "include"`
  * `cache`: `"default" | "no-store" | "reload" | "no-cache" | "force-cache"`
  * `redirect`: `"follow" | "error" | "manual"`
  * `referrer`: URI that linked to the resource being requested
  * `referrerPolicy`: `"" | "no-referrer" | "no-referrer-when-downgrade" | "origin-only" | "origin-when-cross-origin" | "unsafe-url"`
  * `integrity`: HTTP integrity

### Request

Sends a query to a GraphQL endpoint.

**Arguments**

* `query` (required): GraphQL query string
* `variables`: Object with variables

**Response**

* Promise with query response data

### Raw request

Sends a query to a GraphQL endpoint. Returns more information than Request; like extensions, headers, status and GraphQLError.

**Example**

```js
const { rawRequest } = require('graphql-request');

const query = `query getMovie($title: String!) {
  Movie(title: $title) {
    releaseDate
    actors {
      name
    }
  }
}`

const variables = {
  title: 'Inception',
}

rawRequest('https://api.graph.cool/simple/v1/movies', query, variables).then(data => console.log(data))
/* ->
{ data:
   { Movie: { releaseDate: '2010-08-28T20:00:00.000Z', actors: [Array] } },
  headers:
   Headers {
     [Symbol(map)]:
      { 'content-type': [Array],
        'content-length': [Array],
        connection: [Array],
        date: [Array],
        'request-id': [Array],
        server: [Array],
        'x-cache': [Array],
        via: [Array],
        'x-amz-cf-id': [Array] } },
  status: 200 }
*/
```

**Arguments**

* `url` (required): HTTP URL of a GraphQL server
* `query` (required): GraphQL query string
* `variables`: Object with variables

**Response**

* `data` (required): Promise with query response data
* `extensions`: GraphQL response metadata
* `headers` (required): HTTP headers
* `status` (required): HTTP status code
* `errors`: Array of GraphQL syntax errors

## ClientError

**Example**

```js
import { request } from 'graphql-request'

const wrongQuery = `{
  some random stuff
}`

request('my-endpoint', query)
  .then(data => console.log(data))
  .catch(err => {
    console.log(err.response.errors) // GraphQL response errors
    console.log(err.response.data) // Response data if available
  })
```

## Properties

* `response`
  * `errors` (required): Array of GraphQL syntax errors
  * `status` (required): HTTP status code
  * `headers` (required): HTTP headers
* `request`
  * `query` (required): GraphQL query
  * `variables`: GraphQL variables