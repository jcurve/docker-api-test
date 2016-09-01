# What

This repository exists to establish the required configuration settings for our
docker stack to be used in development and test for [secret project]. The tech
stack required will initially just be the api.

We start with:

- Linux
- Ruby
- Postgres
- Redis

# Approach

Current templates to consider:

1. Ruby 2.3.1: https://github.com/docker-library/ruby/blob/2d6449f03976ededa14be5cac1e9e070b74e4de4/2.3/Dockerfile
2. Postgres 9.5.4: https://github.com/docker-library/postgres/blob/fc36c25f8ac352f1fea6d0e7cf8d9bd92a4e720f/9.5/Dockerfile
3. Redis 3.2.3: https://github.com/docker-library/redis/blob/71807ba24f85da5bc14e9251da3617bbb6f47146/3.2/Dockerfile

NB: A common base: the ruby container uses buildpack-deps:jessie, the postgres and redis containers use debian:jessie
buildpack-deps:jessie is based on debian:jessie, but adds numerous base packages. refer:

- https://github.com/docker-library/buildpack-deps/blob/f1d33d5c92e1bd2aee9f2333ceb316251e6388d4/jessie/Dockerfile
- https://github.com/docker-library/buildpack-deps/blob/1845b3f918f69b4c97912b0d4d68a5658458e84f/jessie/scm/Dockerfile
- https://github.com/docker-library/buildpack-deps/blob/a0a59c61102e8b079d568db69368fb89421f75f2/jessie/curl/Dockerfile

and for jessie:

- https://github.com/tianon/docker-brew-debian/blob/589b967ff4364528ebd686b002a6ee00f00f4657/jessie/Dockerfile

THOUGH:
It looks like there is some overlap, but from research, the approach balancing simplicity with minimalism and safety,
plus maintainability, is to use docker compose to combine the 3 seperate containers into a single stack.

If we instead went down the path of trying to minimalize overlap and use a single container, while we reduce some
limited overlap, benefits are minimal. Disk space is cheap, but we lose the ability to rely on the official
containers managed and maintained by the ruby, postgres and redis teams.
For maintainability and reducing internal workload, composing containers from the official repositories and then
having them communicate together seems to be the way to go.

# Strategy

We will build our api app on the current Ruby base image, and use compose to connect the Redis and Postgres containers
and provide a single stack to interact with.

Additional services that we choose to add at a later stage can be added as containers to the compose script.

The api app will be a complete composed stack, which can be later composed with the seperate front end and cdn stacks.
