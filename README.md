
Everything about [Frappe](https://github.com/frappe/frappe) and [ERPNext](https://github.com/frappe/erpnext) in containers.

# Getting Started

open your Docker Desktop

open wsl2 terminal and cd to the path of 'frappe_docker'

run below commands on wsl2
`export APPS_JSON_BASE64=$(base64 -w 0 apps.json)`

`export GIT_AUTH_TOKEN=<get it from Amber>`

Choose the appropriate build command based on your container runtime and desired image type. This command builds the `layered` image with the custom `apps.json` you created.
If you have created this image, you can find it on Docker Desktop.
```sh
docker build \
 --no-cache \
 --build-arg=APPS_JSON_BASE64=$APPS_JSON_BASE64 \
 --build-arg=GIT_AUTH_TOKEN=$GIT_AUTH_TOKEN \
 --tag=custom:3 \
 --file=images/custom/Containerfile .
```

if you  see compose.custom.yaml in your frappe_docker repo, skip below 'docker compose' command
```sh
docker compose --env-file custom.env \
    -f compose.yaml \
    -f overrides/compose.redis.yaml \
    -f overrides/compose.noproxy.yaml \
    config > compose.custom.yaml
```

git clone all apps repos to `/mnt/c/Users/Qiany/erp/apps/`
you can change this path on compose.custom.yaml for you local, so that you can update your code and directly reflect on the container and browser.

start all containers with a single command:
```bash
docker compose -f compose.custom.yaml up -d
```

build site “uptrend” or any name you like with AWS database _d1cf44f2f8d5d74e
you can ignore the exception `Exception: Database _d1cf44f2f8d5d74e already exists` because we are going to use an existing database to keep the DB consistency for every developer.
```sh
docker compose exec backend bench new-site uptrend \
  --db-name _d1cf44f2f8d5d74e \
  --db-password <get it from Amber> \
  --mariadb-user-host-login-scope='%' \
  --db-root-username admin \
  --db-root-password <get it from Amber> 
```
  
When you create your container in first time, run ‘yarn install’ for apps/erpnext and apps/frappe.
You don't need to run them if your apps/erpnext/node_modules and apps/frappe/node_modules exists
```sh
docker compose -f compose.custom.yaml exec backend bash -c "cd /home/frappe/frappe-bench/apps/frappe && yarn install"
docker compose -f compose.custom.yaml exec backend bash -c "cd /home/frappe/frappe-bench/apps/erpnext && yarn install"
```

build your project
```sh
docker compose exec frontend bench build 
```

you will see the site via  `localhost:8080`

# Documentation

### [Getting Started Guide](docs/getting-started.md)

### [Frequently Asked Questions](https://github.com/frappe/frappe_docker/wiki/Frequently-Asked-Questions)

### [Getting Started](#getting-started)

- [Quick Start (Linux/Mac)](docs/01-getting-started/01-quick-start-linux-mac.md)
- [Single Compose Setup](docs/01-getting-started/02-single-compose-setup.md)

### [Setup](#setup)

- [Container Setup Overview](docs/02-setup/01-overview.md)
- [Build Setup](docs/02-setup/02-build-setup.md)
- [Start Setup](docs/02-setup/03-start-setup.md)
- [Environment Variables](docs/02-setup/04-env-variables.md)
- [Compose Overrides](docs/02-setup/05-overrides.md)
- [Setup Examples](docs/02-setup/06-setup-examples.md)
- [Single Server Example](docs/02-setup/07-single-server-example.md)

### [Production](#production)

- [TLS/SSL Setup](docs/03-production/01-tls-ssl-setup.md)
- [Backup Strategy](docs/03-production/02-backup-strategy.md)
- [Multi-Tenancy](docs/03-production/03-multi-tenancy.md)

### [Operations](#operations)

- [Site Operations](docs/04-operations/01-site-operations.md)

### [Development](#development)

- [Development Guide](docs/05-development/01-development.md)
- [Debugging](docs/05-development/02-debugging.md)
- [Local Services Connection](docs/05-development/03-local-services-connection.md)

### [Migration](#migration)

- [Migrate from Multi-Image Setup](docs/06-migration/01-migrate-from-multi-image-setup.md)

### [Troubleshooting](#troubleshooting)

- [Troubleshoot Guide](docs/07-troubleshooting/01-troubleshoot.md)
- [Windows Nginx Entrypoint Error](docs/07-troubleshooting/02-windows-nginx-entrypoint-error.md)

### [Reference](#reference)

- [Build Version 10 Images](docs/08-reference/01-build-version-10-images.md)

# Contributing

If you want to contribute to this repo refer to [CONTRIBUTING.md](CONTRIBUTING.md)

This repository is only for container related stuff. You also might want to contribute to:

- [Frappe framework](https://github.com/frappe/frappe#contributing),
- [ERPNext](https://github.com/frappe/erpnext#contributing),
- [Frappe Bench](https://github.com/frappe/bench).
