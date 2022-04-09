# Install Prerequisites

## git

If you haven't installed git already,
you can probably install the version control system git right from your software manager:

```
sudo apt update
sudo apt install git
```

## Docker

You can install docker [here](https://www.docker.com/community-edition#/download) and docker-compose [here](https://docs.docker.com/compose/install/#prerequisites). If you are not familiar with docker you can read more about it on [their website](https://www.docker.com/what-docker).

## NodeJS

You need the LTS of NodeJS.

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
