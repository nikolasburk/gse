## Nested Mutations

## Step 1: Validate your data model

This section is based on data model that was deployed in [Update Prisma GraphQL API](../2-Update-Prisma-GraphQL-API/README.md). If you data model looks different, either adjust it or quickly [deploy a new service to a Demo server](../1-Setup-Prisma/README.md).

Here is the contents of **datamodel.graphql** your service needs for the following examples to work:

```graphql
type User {
  id: ID! @unique
  name: String!
  posts: [Post!]!
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

## Step 3: Explore nested mutations API

**Create a new `Post` and create a new `User` that is set as the `author` of it**:

```graphql
mutation {
  createPost(data: {
    title: "I love GraphQL"
    author: {
      connect: {
        name: "Karl"
      }
    }
  }) {
    id
    author {
      id
    }
  }
}
```

**Create a new `Post` and set an existing `User` that is set as the `author` of it**:

```graphql
mutation {
  createPost(data: {
    title: "I love GraphQL"
    author: {
      connect: {
        id: "__USER_ID__"
      }
    }
  }) {
    id
  }
}
```