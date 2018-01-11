![Orion](https://static.kevinlin.info/orion-banner.png)

# orion-docker

This repository provides a `docker-compose` file for orchestrating a series of Docker containers that collectively provide all the services that comprise the Orion platform, namely, [`orion-server`](https://github.com/LINKIWI/orion-server) and [`orion-web`](https://github.com/LINKIWI/orion-web).

Please note that Orion's Docker deployment support is considered **experimental**. It is *highly recommended* that you deploy both the server and web interface manually using the instructions at [`orion-server`](https://github.com/LINKIWI/orion-server) and [`orion-web`](https://github.com/LINKIWI/orion-web), respectively. `orion-docker` may be unstable or outdated, and using the Dockerized version is significantly more resource-intensive than deploying the services onto your infrastructure natively.

## Prerequisites

1. You need a [MapBox API token](https://www.mapbox.com/help/how-access-tokens-work/) for your deployment. It is used to render the interactive map in the web interface. They are free.
2. You need [`docker-compose`](https://docs.docker.com/compose/install/).

## Deployment

Get the code:

```bash
$ git clone https://github.com/LINKIWI/orion-docker.git
$ cd orion-docker
```

Create a `.env` file for injecting configuration environment variables into the `docker-compose` orchestration:

```bash
$ cat >.env<<EOF
MAPBOX_API_TOKEN=<your Mapbox API token>
PORT=<local port on which you want Orion to run>
EOF
```

Then, some Docker magic:

```bash
$ docker-compose up -d
```

Orion should now be running at the port you specified in the `.env` file. The easiest way to expose it to the Internet behind a domain name is to reverse proxy via Apache or nginx.

## Additional steps

As noted in the [`orion-server` README](https://github.com/LINKIWI/orion-server/blob/master/README.md), there is no service-level authentication mechanism. **It is still your responsibility to protect routes; otherwise, anonymous users will be able to publish and query location data to and from your Orion instance.** You can do this in whatever way you see fit, but adding a layer of HTTP Basic authentication at the web server layer, while not recommended, is the easiest way to do this (and is compatible with the OwnTracks mobile clients).

## Orchestration

`docker-compose.yml` starts four independent services:

#### MySQL

Used for storing data.

#### `orion-server`

Exposes API endpoints for CRUD operations on the data in the MySQL instance.

#### `orion-web`

Frontend interface that queries `orion-server` for location data to visualize on a map.

#### nginx

Reverse proxies the API routes provided by `orion-server` and the static files provided by `orion-web` on the same host.
