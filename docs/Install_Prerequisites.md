# Install Prerequisites

## git

If you haven't installed git already,
you can probably install the version control system git right from your software manager:

```
sudo apt update
sudo apt install git
```

## Docker

You can [install docker here](https://www.docker.com/community-edition#/download) and [install docker-compose here](https://docs.docker.com/compose/install/#prerequisites). If you are not familiar with docker you can read more about it on [the docker website](https://www.docker.com/what-docker).

### Required docker-compose version

For this project it is recommended and tested to use [docker-compose v1.29.0](https://github.com/docker/compose/releases/tag/1.29.0) and later.

You can inspect the installed version by running ```docker compose version``` if you are using docker-compose V2, or ```docker-compose version``` if you are using docker-compose V1. If you want to install docker-compose via the OS software repository (e. g. via apt), then you can check the provided version by running ```apt-cache policy docker-compose```. If you want to stick with the docker-compose provided by your OS repository, then you can get OwnRecipes running with [docker-compose 1.25.0](https://github.com/docker/compose/releases/tag/1.25.0) and later, though you will need to make few adjustments as written below.

<details>
  <summary>Run OwnRecipes with docker-compose 1.25.0 and higher</summary>
  <p>Open the file "docker-prod.yml". For the service "api", replace and uncomment the section "depends_on" with the variant for older docker-compose versions that is written below in that file.</p>
  <p>Prior:</p>
  <pre>
services:
  db:
    # ...
  api:
    # ...
    # This syntax requires docker-compose v1.29.0+.
    depends_on:
      db:
        condition: service_healthy
  </pre>
  <p>Compatible with docker-compose v1.25.0+:</p>
  <pre>
services:
  db:
    # ...
  api:
    # ...
    # This syntax is compatible with docker-compose v1.25.0+.
    depends_on:
      - db:
  </pre>
  <p>Be aware that this skips the service_healthy-check for the database. As a result, OwnRecipes will start with an invalid db-configuration, like with a wrong password. Once successfully started, this will have no further impact.</p>
</details>

## NodeJS

_You can skip this when running ownrecipes-web via docker._

OwnRecipes supports the latest LTS of NodeJS.

### Installing via the offical PPA

```
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
```
To ensure you get the up-to-date version of node, you'll need to make a modification to your apt-cache policy.

```
sudo nano /etc/apt/preferences.d/nodesource
```
Insert the following:

```
Package: *
Pin: origin deb.nodesource.com
Pin-Priority: 600
```
Then:

```sudo apt-get install nodejs```

### Installing via Node Version Manager

First, install the Node Version Manager (nvm):

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash

export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

Check the installed version, by running:

```
nvm --version
```

Then, install the latest LTS version of node:
```
nvm install --lts
```
