# Creating a Release

## Preparation

1. Update the locales for ownrecipes-web. You might need a release-branch to do so.
    ```bash
    cd OwnRecipes/ownrecipes-web
    npm run locales
    ```
2. Update the env-files for docker-production:
    ```bash
    cd OwnRecipes
    cp ownrecipes-web/.env.production .env.docker.production.web
    cp ownrecipes-api/docs/samples/docker/.env.production .env.docker.production.api
    ```

## GitHub

1. Get an Auth token from GitHub. See [Github Help](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) for details on how to do this.
2. Create a secrets folder and add a file with the following content:
    ```python
    #!/usr/bin/env python
    # encoding: utf-8

    username = 'username'
    password = 'token'
    ```
3. Create a release doc and place in the releases folder. See the release folder for examples.
4. Once you have a release file created, you can run the release script. This script takes the release file you just created as its only argument.
  - `./release x.x.x.json`

## Docker

### ownrecipes-nginx

Checkout the source, build the image, tag it and upload it to docker-hub:

```bash
# Checkout the source
git clone https://github.com/ownrecipes/ownrecipes-nginx --branch <tag>
cd ownrecipes-nginx

# Build the image, tag it
docker build . -t ownrecipes/ownrecipes-nginx:<tag>
docker build . -t ownrecipes/ownrecipes-nginx:latest

# Upload it to docker-hub
docker login
docker push ownrecipes/ownrecipes-nginx:<tag>
docker push ownrecipes/ownrecipes-nginx:latest
```

### ownrecipes-api

Checkout the source, build the image, tag it and upload it to docker-hub:

```bash
# Checkout the source
git clone https://github.com/ownrecipes/ownrecipes-api --branch <tag>
cd ownrecipes-api

# Build the image, tag it
docker build . -t ownrecipes/ownrecipes-api:<tag>
docker build . -t ownrecipes/ownrecipes-api:latest

# Upload it to docker-hub
docker login
docker push ownrecipes/ownrecipes-api:<tag>
docker push ownrecipes/ownrecipes-api:latest
```

### ownrecipes-web

Checkout the source, build the app, build the image, tag it and upload it to docker-hub:

```bash
# Checkout the source
git clone https://github.com/ownrecipes/ownrecipes-web --branch <tag>
cd ownrecipes-web

# Build the app
npm install
npm run build

# Build the image, tag it
docker build . -f Dockerfile-release -t ownrecipes/ownrecipes-api:<tag> .
docker build . -f Dockerfile-release -t ownrecipes/ownrecipes-api:latest .

# Upload it to docker-hub
docker login
docker push ownrecipes/ownrecipes-api:<tag>
docker push ownrecipes/ownrecipes-api:latest
```
