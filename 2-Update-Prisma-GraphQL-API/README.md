# Update Prisma GraphQL API

## Step 1: Adjust your data model

Update the code in **datamodel.graphql** as follows:

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

## Step 2: (Re-)Deploy your Prisma service

```bash
prisma deploy
```

## Step 3: Open a GraphQL Playground

```bash
prisma playground
```

## Step 4: Explore the service's CRUD GraphQL API

**Create a new `Post` and a new `User` as its `author`**:

```graphql
mutation {
  createPost(data: {
    title: "GraphQL is great"
    author: {
      create: {
        name: "Bob"
      }
    }
  }) {
    id
    published
    author {
      id
    }
  }
}
```

**Query all `User`s and their `Post`s**:

```graphql
query {
  users {
    id
    name
    posts {
      id
      title
      published
    }
  }
}
```