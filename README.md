# Spring Boot in Practice

This project can be used as a starter for spring boot api service development, supports both rest and graphql protocol. It is also a reference implementation of [Clean Architecture](https://blog.jaggerwang.net/clean-architecture-in-practice/). This api service can be used as the backend api service for this flutter app [Flutter in Practice](https://github.com/jaggerwang/flutter-in-practice). There is also an article [Spring Boot API 服务开发指南](https://blog.jaggerwang.net/spring-boot-api-service-develop-tour/) for learning this project.

## Dependent frameworks and packages

1. [Spring Boot](https://spring.io/projects/spring-boot) Web framework and server
1. [Spring Data JPA](https://spring.io/projects/spring-data-jpa) Access database
1. [Spring Data Redis](https://spring.io/projects/spring-data-redis) Cache data
1. [Spring Security](https://spring.io/projects/spring-security) Authenticate and authrorize
1. [Spring Session](https://spring.io/projects/spring-session) Manage session
1. [GraphQL Java](https://github.com/graphql-java/graphql-java) Graphql for java
1. [GraphQL Spring Boot Starters](https://github.com/graphql-java-kickstart/graphql-spring-boot) Graphql for spring boot
1. [Flyway](https://flywaydb.org/) Database migration

## APIs

### Rest

| Path  | Method | Description |
| ------------- | ------------- | ------------- |
| /user/register | POST | Register |
| /user/login | POST | Login |
| /user/logout | GET | Logout |
| /user/logged | GET | Get logged user |
| /user/modify | POST | Modify logged user |
| /user/info | GET | Get user info |
| /user/follow | POST | Follow user |
| /user/unfollow | POST | Unfollow user |
| /user/following | GET | Following users of someone |
| /user/follower | GET | Fans of some user |
| /user/sendMobileVerifyCode | POST | Send mobile verify code |
| /post/publish | POST | Publish post |
| /post/delete | POST | Delete post |
| /post/info | GET | Get post info |
| /post/published | GET | Get published posts of some user |
| /post/like | POST | Like post |
| /post/unlike | POST | Unlike post |
| /post/liked | GET | Liked posts of some user |
| /post/following | GET | Posts of following users of someone |
| /file/upload | POST | Upload file |
| /file/info | GET | Get file meta info |

### GraphQL

```graphql
type Query {
    userLogout: User!
    userLogged: User
    userInfo(id: Int!): User!
    userFollowing(userId: Int, limit: Int, offset: Int): [User!]!
    userFollowingCount(userId: Int): Int!
    userFollower(userId: Int, limit: Int, offset: Int): [User!]!
    userFollowerCount(userId: Int): Int!

    postInfo(id: Int!): Post!
    postPublished(userId: Int, limit: Int, offset: Int): [Post!]!
    postPublishedCount(userId: Int): Int!
    postLiked(userId: Int, limit: Int, offset: Int): [Post!]!
    postLikedCount(userId: Int): Int!
    postFollowing(limit: Int, beforeId: Int, afterId: Int): [Post!]!
    postFollowingCount: Int!

    fileInfo(id: Int!): File!
}

type Mutation {
    userRegister(user: UserInput!): User!
    userLogin(user: UserInput!): User!
    userModify(user: UserInput!, code: String): User!
    userSendMobileVerifyCode(type: String!, mobile: String!): String!
    userFollow(userId: Int!): Boolean!
    userUnfollow(userId: Int!): Boolean!

    postPublish(post: PostInput!): Post!
    postDelete(id: Int!): Boolean!
    postLike(postId: Int!): Boolean!
    postUnlike(postId: Int!): Boolean!
}
```

## How to run

This project need java v11+.

### By local environment

#### Clone repository

```bash
git clone https://github.com/jaggerwang/spring-boot-in-practice.git && cd spring-boot-in-practice
```

#### Prepare mysql and redis service

Install mysql and redis server, and start them. After mysql started, create a database for this project, and a user to access this database.

```sql
CREATE DATABASE `sbip`;
CREATE USER 'sbip'@'%' IDENTIFIED BY '123456';
GRANT ALL PRIVILEGES ON `sbip`.* TO 'sbip'@'%';
```

#### Configure application

Change configs in `src/main/resources/application.yml` as your need, especially mysql, redis and path related configs. You can also change configs by environment variables, you need add `SBIP_` prefix to each config you want to change. You should make sure the directories you configured is existing.

#### Start server

```bash
mvn spring-boot:run
```

The running main class is `net.jaggerwang.sbip.api.SbipApplication`. When first start server, it will auto create tables, we use flyway to migrate database changes. You can disable auto create with argument `--spring.flyway.enabled=false`, and then run with argument `--app.type=db_migration` to manually create tables.

After started, the api service's endpoint is `http://localhost:8080/`.

### By docker compose

You need install [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/) at first.

#### Configure compose

Change the content of `docker-compose.yml` as your need, especially the host path of mounted volumes.

#### Start all services

```bash
docker-compose up -d
```

It will start server, mysql and redis services. If you need to stop and remove all services, you can execute command `docker-compose down`. The container port `8080` is mapping to the same port on local host, so the endpoint of api service is same as previous.

When first start mysql, it will auto create a database `sbip` and a user `sbip` with password `123446` to access this database. The password of `root` user is also `123456`.
