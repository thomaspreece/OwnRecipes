# Updating the App

## Running the App with Docker

First pull the latest from the repos:
```bash
cd OwnRecipes
git pull

cd ownrecipes-api
git pull

cd ../ownrecipes-web
git pull
```

Then check the release notes about any changes to the following files:
- docker-prod.override.yml
- ownrecipes-api/.env.production
- ownrecipes-web/.env.production

There should only be changes to these files in major releases (IE. 2.0.0, 3.0.0).

Once you know your env and docker-compose files are up to date, run:

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

Enjoy!

## Updating the app without docker

You managed to run the App without docker, what is pretty cool.
Updating is a bit more involved, as running the App without docker is anyway.

First pull the latest from the repos:
```bash
sudo su ownrecipes

cd OwnRecipes
git pull

cd ownrecipes-api
git pull

cd ../ownrecipes-web
git pull
```

Then check the release notes about any changes to the following files:
- docker-prod.override.yml
- ownrecipes-api/.env.service[.local]
- ownrecipes-api/.env.development[.local]
- ownrecipes-web/.env.service[.local]
- ownrecipes-web/.env.development[.local]

There should only be changes to these files in major releases (IE. 2.0.0, 3.0.0).

Then, [install the python requirements for ownrecipes-api](Running_the_App_Without_Docker_in_dev/#install-the-python-requirements) and [update all the dependencies for ownrecipes-web](Running_the_App_Without_Docker_in_dev/#install-the-dependencies).

Next, apply any [migrations for the api/database](Running_the_App_Without_Docker_in_dev/#populate-the-database).