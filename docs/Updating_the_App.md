# Updating the App

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
