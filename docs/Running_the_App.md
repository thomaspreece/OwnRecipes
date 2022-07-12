# Running the App

**:warning: Following the steps below will install the latest version of OpenEats! :warning:**

**Please note that OwnRecipes does not provide a stable release via docker, yet. If you wish to run OwnRecipes via docker, you will have to compile the docker containers yourself. Please find all needed information in [Running_the_App in dev](Running_the_App_in_dev.md)**

---------------------------------

## Install Prerequisites

Install [git](Install_Prerequisites.md/#git).
Install [docker and docker-compose](Install_Prerequisites.md/#docker).

## Coming from OpenEats?

See the [Migration Guide](Migrating_from_OpenEats.md).

## Setup docker configuration for production

First clone the repos:
```bash
git clone https://github.com/ownrecipes/OwnRecipes.git
cd OwnRecipes
```

Then, copy the needed environment files:
```bash
cp docs/samples/sample_docker_prod_override.yml docker-prod.override.yml
```

### Note on docker-prod.override.yml

The `docker-prod.override.yml` specifies the port that OwnRecipes is served from as well as any additional configuration that overrides the defaults.
It also allows the containers to reboot themselves when your machine restarts or if the containers fail. You can change this to `never` if you want to manually control when the containers start and stop.
By default the nginx docker container will serve as a reverse proxy for the other services, and serve its content on port 8000 on the host machine (forwarded from port 80 on the container).

### Configure the environment file

Most of the settings in your env-files can stay the same. There are a few config settings that need to be changed for most configurations.
See [Setting_up_env_file.md](Setting_up_env_file.md) for a complete description of the environment variables.

`.env.docker.production.api`:

- [MYSQL_USER_PASSWORD](Setting_up_env_file.md#MYSQL_USER_PASSWORD)
- [DJANGO_SECRET_KEY](Setting_up_env_file.md#DJANGO_SECRET_KEY)
- [ALLOWED_HOST](Setting_up_env_file.md#ALLOWED_HOST)
- [NODE_URL](Setting_up_env_file.md#NODE_URL)

`.env.docker.production.web`:

- [REACT_APP_API_URL](Setting_up_env_file.md#REACT_APP_API_URL)


#### Note on the ALLOWED_HOST option

The ALLOWED_HOST option must be set equal to the domain or IP address that users will access the site from.
For example, if you are hosting on "mydomain.com", enter:
``ALLOWED_HOST=mydomain.com``

If you are hosting on `192.168.0.12`, enter:
``ALLOWED_HOST=192.168.0.12``

Note that it is not necessary to specify the port.

#### Note on the REACT_APP_API_URL option

The NODE_API_URL specifies the URL that services use to contact the API.
If you are running OwnRecipes on a port other than 80 or 443, it is necessary to specify this port in the NODE_API_URL option.
E.g. Given that the IP address for the OwnRecipes server is `192.168.0.12` and port is `1234`.

``REACT_APP_API_URL=http://192.168.0.1:1234``

### Connecting to a remote DB
If you are connecting the API to a remote DB (any non-dockerized DB) you need to setup the following configs to your env file.

- [MYSQL_DATABASE](Setting_up_env_file.md#MYSQL_DATABASE)
- [MYSQL_USER](Setting_up_env_file.md#MYSQL_USER)
- [MYSQL_USER_PASSWORD](Setting_up_env_file.md#MYSQL_USER_PASSWORD)
- [MYSQL_HOST](Setting_up_env_file.md#MYSQL_HOST)
- [MYSQL_PORT](Setting_up_env_file.md#MYSQL_PORT)

You will also need to edit your `docker-prod.yml` file to remove the database from the setup process. See [this docker yml](samples/sample_docker_prod_remote_db.yml) for an example.

## Start docker containers

Once the files have been created run the command below and replace the version with version of OwnRecipes you want to run. You can also leave this blank (this will pull the latest code)

```bash
sudo ./quick-start.py -t 1.0.3
```
OR
```bash
sudo ./quick-start.py
```
OR
```bash
sudo ./quick-start.py --help
```

The quick start script will do a few things.
1. Creates a `docker-prod.version.yml` file with the required image tags.
2. Downloads the required images.
3. Takes a backup of the database and your images.
4. Restarts the OwnRecipes servers.

## First Time Setup

To create a super user:
``` bash
sudo docker-compose -f docker-prod.yml run --rm --entrypoint 'python manage.py createsuperuser' api
```
Follow the prompts given to create your user. You can do this as many times as you like.

If you want to add some test data you can load a few recipes and some news data. This data isn't really needed unless you just wanna see how the app looks and if its working.
```bash
sudo docker-compose -f docker-prod.yml run --rm --entrypoint 'sh' api
./manage.py loaddata course_data.json
./manage.py loaddata cuisine_data.json
./manage.py loaddata news_data.json
./manage.py loaddata recipe_data.json
./manage.py loaddata ing_data.json
```

## Setting up a Proxy Server and HTTPS

The nginx reverse proxy will default to run on port 8000. You will most likely want to change the port that nginx runs on.
See [Creating a proxy server for docker](Creating_a_proxy_server_for_docker.md) for more information on how to configure an nginx server to serve OwnRecipes.
