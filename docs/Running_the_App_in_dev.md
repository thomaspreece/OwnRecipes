# Running the app with docker for development

<details>
  <summary>⚠ Note for Windows users</summary>
  <p>
    This documentation is written for Linux-based systems.
    If you are using Windows, please be aware of some subtle changes:
    <ol>
      <li>Do not use the built-in commad-line, but the PowerShell. Some syntax will not work on the command-line.</li>
      <li>There is no sudo. Most of the commands will work without the sudo. If you encounter permission errors, please run your PowerShell as administrator.</li>
    </ol>
  </p>
</details>

## Install Prerequisites

Install [docker and docker-compose](Install_Prerequisites.md/#docker).
Install [git](Install_Prerequisites.md/#git).

## Setup OwnRecipes

First clone the repos:
```bash
git clone https://github.com/ownrecipes/OwnRecipes.git
cd OwnRecipes

git clone https://github.com/ownrecipes/ownrecipes-api.git
git clone https://github.com/ownrecipes/ownrecipes-web.git
```

Then run it:
```bash
sudo docker-compose --profile all build
sudo docker-compose --profile all up
```

## First Time Setup

Seed the database.

To create a super user:
``` bash
sudo docker-compose run --rm --entrypoint 'python manage.py makemigrations' api
sudo docker-compose run --rm --entrypoint 'python manage.py migrate' api
sudo docker-compose run --rm --entrypoint 'python manage.py createsuperuser' api
```
Follow the prompts given to create your user. You can do this as many times as you like.

If you want to add some test data you can load a few recipes and some news data. This data isn't really needed unless you just wanna see how the app looks and if its working.
```bash
sudo docker-compose run --rm --entrypoint 'sh' api
./manage.py loaddata course_data.json
./manage.py loaddata cuisine_data.json
./manage.py loaddata news_data.json
./manage.py loaddata recipe_data.json
./manage.py loaddata ing_data.json
```

## Finish up

The set up is complete and everything should be up and running.

You can visit the [Admin Site](Admin_site.md), to create some more users, customize the news, or manage some lists.

Or you can straight away log in to the OwnRecipes web app. By default, the url will be `http://localhost:8080`.

OwnRecipes will shut down with your system. You can simply launch OwnRecipes by running:
```bash
cd OwnRecipes
sudo docker-compose --profile all up
```

## Updating to a new (develop) version

When updating your local version that is deployed via docker,
you have to keep in mind that the installed dependencies
are being cached to reduce startup times.

Thus, when you update your local version and the new version
requires new dependencies (or new package versions), you will
have to clean up the related local docker stuff.

First, clean up everything:
```bash
cd OwnRecipes
sudo docker-compose --profile all down
```

Then, update your local repositories:
```bash
cd ownrecipes-api
git checkout development
git pull

cd ../
cd ownrecipes-web
git checkout development
git pull
```

Finally, [build and run OwnRecipes](#setup-ownrecipes), as you usually would.
