# Docker compose commands Documentation

## Overview
This doc is designed for managing a Docker-based project with the ID `qgis-plugins`. It includes various commands for building, running, and maintaining both production and development environments. Below is a detailed description of each command available in the Makefile.

## Commands

### Production Commands

- **default**: Alias for the build command.
```sh
make
```

- **run**: Builds the project and runs the web, migrate, and collectstatic commands.
```sh
make run
```

- **build**: Builds Docker images for both production and development environments.
```sh
make build
```

- **db**: Starts the database container in production mode.
```sh
make db
```

- **metabase**: Starts the Metabase container after ensuring the database is running.
```sh
make metabase
```

- **web**: Starts the web container and scales the `uwsgi` service to 2 instances.
```sh
make web
```

- **certbot**: Starts the Certbot container for managing SSL certificates.
```sh
make certbot
```

- **migrate**: Runs database migrations, with the `auth` app being migrated first.
```sh
make migrate
```

- **update-migrations**: Creates new migration files based on changes in models.
```sh
make update-migrations
```

- **collectstatic**: Collects static files for the Django application.
```sh
make collectstatic
```

- **start**: Starts a specific container or all containers. Specify the container with the `c` variable.
```sh
make start c=container_name
```

- **restart**: Restarts a specific container or all containers. Specify the container with the `c` variable.
```sh
make restart c=container_name
```

- **kill**: Stops a specific container or all containers. Specify the container with the `c` variable.
```sh
make kill c=container_name
```

- **rm**: Removes all containers after stopping them.
```sh
make rm
```

- **rm-only:** Removes all containers without stopping them first.
```sh
make rm-only
```

- **dbrestore:** Restores the database from a backup file.
```sh
make dbrestore
```

- **wait-db:** Waits for the database to be ready.
```sh
make wait-db
```

- **create-test-db:** Creates a test database with PostGIS extension.
```sh
make create-test-db
```

- **rebuild_index:** Rebuilds the search index for the Django application.
```sh
make rebuild_index
```

- **uwsgi-shell:** Opens a shell in the `uwsgi` container.
```sh
make uwsgi-shell
```

- **uwsgi-reload:** Reloads the Django project in the `uwsgi` container.
```sh
make uwsgi-reload
```

- **uwsgi-errors:** Tails the error logs in the `uwsgi` container.
```sh
make uwsgi-errors
```

- **uwsgi-logs:** Tails the requests logs in the `uwsgi` container.
```sh
make uwsgi-logs
```

- **web-shell:** Opens a shell in the NGINX/web container.
```sh
make web-shell
```

- **web-logs:** Tails the logs in the NGINX/web container.
```sh
make web-logs
```

- **logs:** Tails logs for a specific container or all containers. Specify the container with the `c` variable.
```sh
make logs c=container_name
```

- **shell:** Opens a shell in a specific container. Specify the container with the `c` variable.
```sh
make shell c=container_name
```

- **exec:** Executes a specific Docker command. Specify the command with the `c` variable.
```sh
make exec c="command"
```

### Development Commands

These targets explicitly load `docker-compose.yml` and `docker-compose.dev.yml`,
without loading `docker-compose.override.yml`. The development image is tagged
`qgis-plugins-dev:local`, separately from the production image. Set `DEBUG=True`
yourself in `.env` for Django to serve source static files and the development
bundles. The setup preserves your `DEBUG` setting.

- **build-dev:** Builds the local development image shared by `devweb`, webpack, and `maindev`.
```sh
make build-dev
```

- **devweb-test:** Starts the `devweb` container for testing, ensuring the database is running.
```sh
make devweb-test
```

- **devweb-assets:** Stops the webpack watcher, installs locked npm dependencies in a Docker volume, and builds development CSS/JavaScript. A failed build stops startup.
```sh
make devweb-assets
```

- **devweb:** Builds frontend assets, then starts the `devweb` container for development, along with RabbitMQ, worker, beat, and webpack containers.
```sh
make devweb
```

- **devweb-migrate:** Runs database migrations in the `devweb` container.
```sh
make devweb-migrate
```

- **devweb-makemigrations:** Creates new migration files based on changes in models in the `devweb` container.
```sh
make devweb-makemigrations app=app_name
```

- **devweb-exec:** Executes a specific Docker command in the `devweb` container. Specify the command with the `c` variable.
```sh
make devweb-exec c="command"
```

- **devweb-shell:** Opens a shell in the `devweb` container.
```sh
make devweb-shell
```

- **devweb-runserver:** Prepares frontend assets, starts the webpack watcher, and runs Django at http://localhost:62202 (host port `62202` maps to container port `8081`).
```sh
make devweb-runserver
```

With `DEBUG=True` set in `.env`, LiveReload is enabled on pages opened at
`http://localhost:62202`. Development JavaScript connects to
`ws://localhost:35729/livereload`; port `35729` runs a
separate LiveReload service. Opening `http://localhost:35729/` displays
`{"tinylr":"Welcome","version":"1.1.1"}`, which is the expected service response.
To check LiveReload, edit a bundled JS/SCSS file and wait for webpack to compile:
the website should refresh automatically. In browser DevTools, the LiveReload
WebSocket should have status `101`. On WSL mounts under `/mnt/c`, set
`WATCHPACK_POLLING=true` in `.env` if edits are not detected.

The LiveReload port is published only on the local host. Asset preparation
stops the watcher before reinstalling dependencies; if it fails, fix the error
and rerun `make devweb-runserver` to restart it.

Chrome can log “Page entered Back-Forward Cache” when it suspends the LiveReload
WebSocket during navigation. Reload the page after returning; this message alone
does not indicate an asset build failure.

- **dbseed:** Seeds the database with initial data from JSON files in the `fixtures` directory.
```sh
make dbseed
```
