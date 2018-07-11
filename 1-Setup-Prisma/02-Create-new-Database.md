# Create new database

## Step 1: Install the Prisma CLI on your machine

```bash
npm install -g prisma
```

## Step 2: Install Docker on your machine

To use Prisma locally, you need to have [Docker](https://www.docker.com) installed on your machine. If you don't have Docker yet, you can download the Docker Community Edition for your operating system [here](https://www.docker.com/community-edition).

## Step 3: Setup database and Prisma server

### 3.1: Create the Docker Compose file

```bash
touch docker-compose.yml
```

### 3.2: Add Prisma and database Docker images

Paste the following contents into `docker-compose.yml`:

```yml
version: '3'
services:
  prisma:
    image: prismagraphql/prisma:1.11
    restart: always
    ports:
    - "4466:4466"
    environment:
      PRISMA_CONFIG: |
        port: 4466
        databases:
          default:
            connector: mysql
            host: mysql
            port: 3306
            user: root
            password: prisma
            migrations: true
  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: prisma
    volumes:
      - mysql:/var/lib/mysql
volumes:
  mysql:
```

<Details>
  <Summary>See `docker-compose.yml` for Postgres</Summary>

Here is what the `docker-compose.yml` looks like for Postgres:

```yml
version: '3'
services:
  prisma:
    image: prismagraphql/prisma:1.11
    restart: always
    ports:
    - "4466:4466"
    environment:
      PRISMA_CONFIG: |
        port: 4466
        # uncomment the next line and provide the env var PRISMA_MANAGEMENT_API_SECRET=my-secret to activate cluster security
        # managementApiSecret: my-secret
        databases:
          default:
            connector: postgres
            host: postgres
            port: 5432
            user: prisma
            password: prisma
            migrations: true
  postgres:
    image: postgres
    restart: always
    environment:
      POSTGRES_USER: prisma
      POSTGRES_PASSWORD: prisma
    volumes:
      - postgres:/var/lib/postgresql/data
volumes:
  postgres:
```

</Details>

### 3.3: Start Prisma server and database

Run the following command in your terminal:

```bash
docker-compose up -d
```

Your Prisma _server_ is now running on `http://localhost:4466`, so you can deploy Prisma _services_ to it.

## Step 4: Create your Prisma service configuration

```bash
mkdir hello-world
cd hello-world
prisma init --endpoint http://localhost:4466
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