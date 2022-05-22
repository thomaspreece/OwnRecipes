## Configuing your Environment

This file will provide some context on the env settings.


## Database config

The following environment variables can be applied the ownrecipes-api/.env files.

Docker configurations come with MariaDB. We only need to set the following options, the rest are not required.
For a full list of settings see: https://hub.docker.com/_/mariadb/

#### MYSQL_DATABASE
The database name.

EX: `MYSQL_DATABASE=ownrecipes`

#### MYSQL_USER
The user for the database.

EX: `MYSQL_USER=ownrecipes`

#### MYSQL_USER_PASSWORD
The password for the user given above.

EX: `MYSQL_USER_PASSWORD=root`

#### MYSQL_ROOT_PASSWORD
Deprecated, use MYSQL_USER_PASSWORD instead.
The password for the user given above.

EX: `MYSQL_ROOT_PASSWORD=root`

#### MYSQL_HOST
The address or hostname of the DB. Do not include this in your env file if you are using docker to house the DB.

EX: `MYSQL_HOST=my.db.com`

#### MYSQL_PORT
The port the database is exposed on.

EX: `MYSQL_PORT=3306`


## API / Django config

The following environment variables can be applied the ownrecipes-api/.env files.

#### API_URL
This URL and port combination is used by gunicorn to serve the API.
For docker instances we need to serve via `0.0.0.0`.
`0.0.0.0` means "all IPv4 addresses on the local machine".
If a host has two IP addresses, 192.168.1.1 and 10.1.2.1,
and a server running on the host listens on 0.0.0.0,
it will be reachable at both of those IPs.

EX: `API_URL=0.0.0.0:5210`

#### API_PORT
The port the API is served from.

EX: `API_PORT=5210`

#### DJANGO_SECRET_KEY
A secret key for a particular Django installation.
This is used to provide cryptographic signing, and should be set to a unique, unpredictable value.
You can create one yourself or use a generator to do so.

For more information, see: https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-SECRET_KEY

EX: `DJANGO_SECRET_KEY=sdfsadfas32e98zsdvhhsnz6udvbksjdhfi4galshjfg`

#### DJANGO_SETTINGS_MODULE
The settings file that django will use.

EX: `DJANGO_SETTINGS_MODULE=base.settings`

#### DJANGO_DEBUG
Set the debug environment of the Django app.
This should be set to `False` in production and `True` to debug any issues.

EX: `DJANGO_DEBUG=False`

#### ALLOWED_HOST
The hostname that the API is being served from.

For more information, see: https://docs.djangoproject.com/en/1.11/ref/settings/#allowed-hosts

EX: `ALLOWED_HOST=ryannoelk.com`

#### NODE_URL
The URL and port node is served from.
The API will use this to prevent CORS issues.

EX: `NODE_URL=localhost:8080`

#### HTTP_X_FORWARDED_PROTO
If you are serving content behind an HTTPS proxy.
Set this to `True`, otherwise `False`.
If you are using the docker configuation, set this to `True`.

For more information, see: https://docs.djangoproject.com/en/1.10/ref/settings/#secure-proxy-ssl-header

EX: `HTTP_X_FORWARDED_PROTO=False`

#### ADMIN_URL
The url the Django Admin Pages should be served from. Default is `admin`.

EX: `ADMIN_URL=ownrecipes-admin`

#### SITE_MEDIA_URL
The url the media filse should be served from. Default is `site-media`.

EX: `ADMIN_URL=ownrecipes-site-media`

#### STATIC_FILES_URL
The url the static files, like css files, should be served from. Default is `static-files`.

EX: `ADMIN_URL=ownrecipes-static-files`


## Web config

The following environment variables can be applied the ownrecipes-web/.env files.

#### REACT_APP_API_URL
The hostname/port (my.example.com:5210) the frontend will call the API from.
If unset, the UI will call the API from the same hostname/port. If you are not using the default Nginx server that OwnRecipes comes with, you will either need to set this or configure your own proxy server to redirect all traffic that starts with `/api` or `/admin`.

EX: `REACT_APP_API_URL=http://localhost:5210`, `REACT_APP_API_URL=https://api.example.com`

### REACT_APP_ADMIN_URL
If you have set up a different url for the DJANGO admin page, then you will have to set this variable accordingly.

EX: `REACT_APP_API_URL=http://localhost:5210/some-admin-path`, `REACT_APP_API_URL=https://admin.api.example.com`

### REACT_APP_REQUIRE_LOGIN
If you are storing recipes that would otherwise cause Copyright infringement, or you generally don't want strangers to view your recipes, then you can make OwnRecipes require a login to use any content. If set to `true`, users that are not logged in will be redirected to the login page.

EX: `REACT_APP_REQUIRE_LOGIN=true`

#### REACT_APP_LOCALE
The language the UI will be in.

Options:
- English: en
- German: de

EX: `NODE_LOCALE=de`
