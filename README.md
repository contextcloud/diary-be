# What this mess is
Context Cloud needs a sample "customer's" application to demonstrate its deployment into the platform.

This repo is the back-end of such Demo app, which is an extremely simple web application, implementing an online diary.
There is no authentication to this app, the only screen allows to list the last few entries and create a new one.
The app uses Postgres DB for persistence.

# Running the app locally
The app is wrapped into a Docker image and pushed to docker hub to be able to run it directly without building.  
There is a `docker-compose.yml` file in the root directory, which can be used to start both the back-end app and the database.
```shell
docker compose up
```

alternatively use the `run.sh` and `stop.sh` scripts.


# Calling the APIs

There's two endpoints to call

## Get Items

GET http://localhost:8080/api/entries

```shell
curl http://localhost:8080/api/entries
```

Sample response:

```json
[
  {
    "ts": 1675846708178,
    "dateTime": "08/02/2023 08:58",
    "text": "two"
  },
  {
    "ts": 1675846702721,
    "dateTime": "08/02/2023 08:58",
    "text": "one"
  }
]
```

## Create an Entry

POST http://localhost:8080/api/entry

```shell
curl -X POST http://localhost:8080/api/entry \
  -H "Content-Type: application/json" \
   -d '{"text": "first one!!!"}'
```

Response is an empty HTTP 200 (for now).  
The text will be truncated to first 250 symbols.

# Building
## Pre-requisites
* Java 17
* Gradle
* Be a contributor for `contextcloud` organization on GitHub.

## Build only

The `build.sh` script will build the app and package it into Docker (locally) with tag `contextcloud/diary-be:latest`

```shell
./build.sh
```

## Build and Push

To build and push to Context Cloud repo on GitHub, run

```shell
build-and-push.sh
```

# Environment Variables for Deployment
When Context Cloud deploys the app, it needs to hook it up to the database, which will be provisioned by ContextCloud rather than the one defined in the `docker-compose.yml`  
To define RDS DB connection parameters this application watches the following three environment variables:

| Env. Var | Default value (fallback) |
|----------|--------------------------|
|RDS_DB_URL|jdbc:postgresql://diary-db:5432/diary|
|RDS_DB_USERNAME|diary_admin|
|RDS_DB_PASSWORD|123456789|

See `docker-compose.yml` for details about fallback values and how they are used.  
Postgres version used is 14.