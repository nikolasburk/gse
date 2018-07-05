## Filters & Sorting

## Step 1: Validate your data model

This section is based on data model that was deployed in [Update Prisma GraphQL API](../2-Update-Prisma-GraphQL-API/README.md). If your data model looks different, either adjust it or quickly [deploy a new service to a Demo server](../1-Setup-Prisma/README.md).

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

**Filter for all `User`s that have the letter `S` in their `name`**:

```graphql
query {
  users(where: {
    name_contains: "S"
  }) {
    id
    name
  }
}
```

**Filter for all `User`s that either have the letter `S` _or_ the letter `P` in their `name`**:

```graphql
query {
  users(where: {
    OR: [
      { name_contains: "S" },
      { name_contains: "P" },
    ]
  }) {
    id
    name
  }
}
```

**Filter for all `User`s but only include those `posts` where the `title` is either of three srtrings: `GraphQL`, `REST`, `API`**:

```graphql
query {
  users {
    id
    name
    posts(where: {
      title_in: ["GraphQL", "REST", "API"]
    }) {
      id
      title
    }
  }
}
```

**Query all `Post`s written by `User` with `id` `cjj8q47xvb0nl0b82j0qy4nii`**:

```graphql
query {
  posts(where: {
    author: {
      id: "cjj8q47xvb0nl0b82j0qy4nii"
    }
  }) {
    id
  }
}
```

## Step 4: Explore ordering API

**Sort `Post`s alphabetically descending by their `title`**:

```graphql
query {
  posts(orderBy: title_DESC) {
    id
    title
  }
}
```

**Sort `User`s chronologically ascending by when they were _created_**:

```graphql
query {
  users(orderBy: createdAt_ASC) {
    id
    name
  }
}
```