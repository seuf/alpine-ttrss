# TTRSS Dockerfile

Tiny Tiny RSS is an open source web-based news feed (RSS/Atom) reader and aggregator, designed to allow you to read news from any location, while feeling as close to a real desktop application as possible.

This is a Dockerfile to build a ttrss image.

## Build

Just run the docker build command :

```
docker build -t seuf/ttrss .
```

## Run

This image require a MySQL / MariaDB database
Here is an example of docker compose tu run ttrss :


```
version: '2'

services:

  traefik:
    image: traefik
    hostname: traefik
    container_name: traefik
    command: --web --docker --docker.domain=example.com --logLevel=DEBUG --acme --acme.email=foo@example.com --acme.storage=/etc/traefik/acme.json --entryPoints='Name:http Address::80' --defaultentrypoints=http
    ports:
      - "80:80"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "/dev/null:/traefik.toml"

  mariadb:
    image: seuf/mariadb
    hostname: mariadb
    environment:
      DATABASES: "ttrss"
      DB_USER_ttrss: "ttrss"
      DB_PASS_ttrss: "ttrss_password"
    volumes:
      - "/data/docker/volumes/mariadb:/var/lib/mysql"
    labels:
      - "traefik.enable=false"


  ttrss:
    image: seuf/ttrss
    environment:
      TTRSS_DB_HOST: mariadb
      TTRSS_DB_PORT: 3306
      TTRSS_DB_NAME: ttrss
      TTRSS_DB_USER: ttrss
      TTRSS_DB_PASS: ttrss_password
      TTRSS_SELF_URL_PATH: http://rss.example.com
    labels:
      - "traefik.frontend.rule=Host:rss.example.com"
```
