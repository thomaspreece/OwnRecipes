# Running the App: Tricks

Below some tips and tricks for running the app for some edge-cases.

## Web-Developer? Run the api via docker, and the web via npm

If you just want to run ownrecipes-web in development without docker, and actually don't mind to set up ownrecipes-api via docker, then that may be actually way easier then setting up everything without docker (because getting the api up to running is quite involved).

To only set up the api + MariaDB via docker, follow the guide [Running the App in development](Running_the_App_in_dev.md).

You can only run the api + MariaDB like this:

_Note: This will require docker-compose version 1.28+, as it makes use of the
new profiles-feature._

```bash
cd OwnRecipes
sudo docker-compose build
sudo docker-compose up
```

Then, [set up the web without docker for development](Running_the_App_Without_Docker_in_dev.md/#ownrecipes-web). If you didn't change the environment-files for ownrecipes-api, then you probably don't need to for ownrecipes-web, too! \o/

Just start the web in development and you should be ready to rumble:
```bash
cd ownrecipes-web
npm start
```

## Single-board computer

I am running OwnRecipes on my Raspberry Pi 3, and it is performing quite well.

Key is to [run it without docker](Running_the_App_Without_Docker).

You can even host OwnRecipes under a sub-path, like https://my-raspberry-pi.com/ownrecipes.
First, make sure that your [web-server](Running_the_App_Without_Docker/#web-server-nginx-option-2) is really listening for / serving the right sub-path accordingly. I use nginx over apache, as I found it to perform better.

Then, change the file `ownrecipes-web/package.json`,
that the config `homepage` is pointing to your domain and sub-path, for example:
```json
{
  "...": "...",
  "homepage": "https://my-raspberry-pi.com/ownrecipes",
  "...": "...",
}
```

Build that:
```bash
cd ownrecipes-web
npm run build
```

And copy the result to the directory that your web-server is serving.

**Tip:** As building the web takes pretty long on my Raspberry Pi, I rather do that on my desktop computer, and copy the result via scp to my Raspberry Pi. You don't need NodeJS on your Raspberry Pi to run the web,
as the app is compiled to a static website. Make sure that the `.env.production.local` is fed with correct values, or create a separate file `.env.raspi`. Build the app with `REACT_APP_ENV=raspi npm run build:prod`.
