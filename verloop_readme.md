## Context

In verloop, we plan on using Vector-admin to view our Vector database/s on our local machine for quick debugging purposes.
This will allow us to view the text and metadata of objects in a class/collection quickly in a pinch. It will also allow us to visualize our schema
better and release better versions in the future.

This repo is to be locally hosted, so no resources are needed other than the local machine and port-forwarding permissions.

## Pre-requisites

1. `docker` and `docker compose`

Note:`docker-compose` is an older version and `docker compose` is preferred. Please refer to official documentation to install it in local machine.

2. kubectl Portforwarding permissions

## Set-up

- `cd vector-admin`
- `git checkout verloop` (Make sure to be in verloop branch)
- `cd docker/`
- `cp .env.example .env`.
- Edit `.env` file and update the variables. **please** update all of the following:
```shell
JWT_SECRET="some-random-string"
INNGEST_SIGNING_KEY="some-random-string"
```

## Run vector-admin
- We need a running Postgres DB to get vector-admin up and running. WE will use a containerized postgres that uses the local network.
This means that first if we have a local postgres instance running, we need to make sure that it is stopped so that port `5432` is vacant.
How to check in linux?

- `sudo lsof -i :5432`

If you see a result popping up, then run the following:

- `sudo service postgresql stop`

Note: You would have to stop local postgres like this every time you restart your machine, before running the commands below.

**Run containerized Postgres DB**

Run this command first to get a dockerized Postgres container running:

- `sudo docker compose up -d --build postgres`

Note: You may not need `sudo`

**Build vector-admin docker image from source**

Run this command:

- `sudo docker compose up -d --build vector-admin`

Note: The first time building may take some time.

In another terminal window, run the following to use the dev env:

- `kubectl config use-context dev`

Then, run weaviate port-forwarding in the same window:

- `kubectl port-forward --namespace weaviate weaviate-0 8080:8080`

Note: Make sure you have relevant permissions


Now, if you open your browser window and go to "http://localhost:3001" you should be greeted by vector-admin landing page.
You can set up your workspace as "verloop" and in the page where you are supposed to connect your vector database, make sure to enter:

- url: "http://127.0.0.1:8080"

Note: No password is required. Connection should be established without it.

- In the main dashboard, once sync button is pressed, syncing should begin.

## Cleanup

Once we are done using vector-admin, we can stop port-forwarding window by `CTRL+C`. After that,

- `cd vector-admin`
- `cd docker/`

**Stop containerized Postgres DB**

Running this command stops both the vector-admin and docker containers gracefully:

- `sudo docker compose down -v postgres`

