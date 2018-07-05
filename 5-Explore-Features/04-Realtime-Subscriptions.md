## Realtime Subscriptions

## Step 1: Validate your data model

This section is based on data model that was deployed in [Update Prisma GraphQL API](../2-Update-Prisma-GraphQL-API/README.md). If you data model looks different, either adjust it or quickly [deploy a new service to a Demo server](../1-Setup-Prisma/README.md).

Here is the contents of **datamodel.graphql** your service needs for the following examples to work:

```graphql
type User {
  id: ID! @unique
  name: String!
  posts: [Post!]! # does this work or requires `-f` when deploying?
}

type Post {
  id: ID! @unique
  title: String!
  published: Boolean! @default(value: "false")
  author: User
}
```

## Step 2: Open a GraphQL Playground

```bash
prisma playground
```

## Step 3: Explore filter API

TBD