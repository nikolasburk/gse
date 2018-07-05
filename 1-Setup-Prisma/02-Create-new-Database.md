# Create new database

## Step 1: Install the Prisma CLI on your machine

```bash
npm install -g prisma
```

## Step 2: Install Docker on your machine

To use Prisma locally, you need to have [Docker](https://www.docker.com) installed on your machine. If you don't have Docker yet, you can download the Docker Community Edition for your operating system [here](https://www.docker.com/community-edition).

## Step 3: Create your Prisma service

```bash
mkdir hello-world
cd hello-world
prisma init
```

After running `prisma init`, the Prisma CLI prompts you to select _where_ you want to deploy your Prisma service:

1. Select **Create new database**
1. Select either **MySQL** or **PostgreSQL**

## Step 4: Set up local Prisma server

```bash
docker-compose up -d
```

## Step 5: Deploy your Prisma service

```bash
prisma deploy
```

## Step 6: Open a GraphQL Playground

```bash
prisma playground
```

## Step 7: Explore the service's CRUD GraphQL API

**Create a new `User`**:

```graphql
mutation {
  createUser(data: {
    name: "Alice"
  }) {
    id
  }
}
```

> Copy the `id` of the `createUser` object returned in the Playground and use it for the `__USER_ID__` placeholer value in the following `updateUser`- and `deleteUser`-mutations.

**Query all `User`s**:

```graphql
query {
  users {
    id
    name
  }
}
```

**Update a `User`'s `name`**:

```graphql
query {
  updateUser(
    where: { id: "__USER_ID__" },
    data: { name: "Sarah" }
  ) {
    id
    name
  }
}
```

**Delete a `User`**:

```graphql
query {
  deleteUser(where: {
    id: "__USER_ID__"
  }) {
    id
    name
  }
}
```