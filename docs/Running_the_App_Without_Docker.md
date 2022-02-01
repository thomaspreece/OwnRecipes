*Please note, most every line that contains commands will require either running as root or sudo*

## Install Dependencies

`apt-get install python3 python3-pip git build-essential`

## Nodejs

### Install Nodejs

```
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
```
To ensure you get the up-to-date version of node, you'll need to make a modification to your apt-cache policy.

```
nano /etc/apt/preferences.d/nodesource
```
Insert the following:

```
Package: *
Pin: origin deb.nodesource.com
Pin-Priority: 600
```
Then:

```sudo apt-get install nodejs```

## Database

We will use MariaDB.

### Install MariaDB

```
apt install mariadb-server
```
Don't forget to run `mysql_secure_installation`!

### Install Additional Dependencies

`apt install default-libmysqlclient-dev libssl-dev`

### Create OwnRecipes User and Database in MariaDB (ssh)

#### Log Into Your MySQL Database

`mysql`

#### Create Database

`CREATE DATABASE ownrecipes;`

#### Create a Database User and Password

*Change username and password. You will have to change that in the configuration files, too.*

*It is recommended to stick to the username "ownrecipes" to minimize the required changes in the configuration files.*

`GRANT ALL PRIVILEGES ON ownrecipes.* TO 'username'@'localhost' IDENTIFIED BY 'password';`

Example:

`GRANT ALL PRIVILEGES ON ownrecipes.* TO 'ownrecipes'@'localhost' IDENTIFIED BY '<password>';`

#### Commit Changes

`FLUSH PRIVILEGES;`

#### Exit From MySQL

`EXIT;`

## Create Directory Infrastructure

*It is recommended to stick to the path "/opt/ownrecipes(/ownrecipes-api)" to minimize the required changes in the configuration files.*

```
mkdir /opt/ownrecipes/
mkdir /opt/ownrecipes/ownrecipes-apache2/
```

## Set up the ownrecipes-api

`cd /opt/ownrecipes`

### Get the Source of ownrecipes-api from Github

```
git clone https://github.com/ownrecipes/ownrecipes-api.git
cd /opt/ownrecipes/ownrecipes-api
```

### Install the Python Requirements

`pip3 install -U -r /opt/ownrecipes/ownrecipes-api/base/requirements.txt`

### Create .env Environment File

**localhost:**

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/localhost/.env /opt/ownrecipes/ownrecipes-api/`

**server:**

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/server/.env /opt/ownrecipes/ownrecipes-api/`

**proxy:**

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/proxy/.env /opt/ownrecipes/ownrecipes-api/`

Open the file `/opt/ownrecipes/ownrecipes-api/.env` and change the [variables](docs/Setting_up_env_file.md) to your needs.

### Copy the /opt/ownrecipes/ownrecipes-api/base/prod-entrypoint.sh file

*If you are using a different directory than "/opt/ownrecipes", then you have to adjust the file to your needs.*

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/prod-entrypoint.sh /opt/ownrecipes/ownrecipes-api/base/`

### Edit the /opt/ownrecipes/ownrecipes-api/base/settings.py file

Find:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': os.environ.get('MYSQL_DATABASE', 'ownrecipes'),
        'USER': os.environ.get('MYSQL_USER', 'username'),
        'PASSWORD': os.environ.get('MYSQL_ROOT_PASSWORD', 'password'),
        'HOST': os.environ.get('MYSQL_HOST', 'db'),
        'PORT': os.environ.get('MYSQL_PORT', '3306'),
        'OPTIONS': {
            'charset': 'utf8mb4'
        },
        'TEST': {
            'NAME': os.environ.get('MYSQL_TEST_DATABASE', 'test_ownrecipes')
        }
    }
}
```

Edit this to include the same options you set in your `.env`.

Example:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': os.environ.get('MYSQL_DATABASE', 'ownrecipes'),
        'USER': os.environ.get('MYSQL_USER', 'ownrecipes'),
        'PASSWORD': os.environ.get('MYSQL_ROOT_PASSWORD', 'db-trustNo1'),
        'HOST': os.environ.get('MYSQL_HOST', 'db'),
        'PORT': os.environ.get('MYSQL_PORT', '3306'),
        'OPTIONS': {
            'charset': 'utf8mb4'
        },
        'TEST': {
            'NAME': os.environ.get('MYSQL_TEST_DATABASE', 'test_ownrecipes')
        }
    }
}
```

### Copy the /opt/ownrecipes/ownrecipes-api/base/gunicorn_start.sh file

*If you are using a different directory than "/opt/ownrecipes", or can't use the os user "ownrecipes", then you have to adjust the file to your needs.*

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/gunicorn_start.sh /opt/ownrecipes/ownrecipes-api/base/`

### Create systemd service to run the api

*If you are using a different directory than "/opt/ownrecipes", or can't use the os user "ownrecipes", then you have to adjust the file to your needs.*

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/ownrecipes.service /lib/systemd/system/`

### Populate the database

*This step is optional.*

```
cd /opt/ownrecipes/ownrecipes-api
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser

python manage.py loaddata course_data.json
python manage.py loaddata cuisine_data.json
python manage.py loaddata news_data.json
python manage.py loaddata recipe_data.json
python manage.py loaddata ing_data.json
```

### Create the OS user

```
sudo useradd -m -d /opt/ownrecipes username
sudo chown -R username:groupname /opt/ownrecipes
```

### Start the api

`systemctl start ownrecipes.service`

### Check if the api running correctly

`systemctl status ownrecipes.service`

## Set up the ownrecipes-web

`cd /opt/ownrecipes/`

### Get the source of ownrecipes-web from git

```
git clone https://github.com/ownrecipes/ownrecipes-web.git
cd /opt/ownrecipes/ownrecipes-web/
```

### Create environment file in /opt/ownrecipes-web/

`touch /opt/ownrecipes/ownrecipes-web/.env.local`

### Edit the created files .env.local and insert the following data in it

*Read the comments carefully and change the necessary parts to your configuration.*

Our api will still listen on port **5210**

```
# url to your backend
export REACT_APP_API_URL=https://ownrecipes.domain.com:5210
# default language to use
export REACT_APP_LOCALE=en
```

### Install the dependecies

```
npm install
```

### Build ownrecipes-web

`npm run build`

This will create the build directory as `/opt/ownrecipes/ownrecipes-web/build`


## Install Web-Server: Apache 2 (Option 1)

**Info: If you prefer nginx, stick to the second option below and skip this step.**

**Info: The docker version uses nginx.**

`apt-get install apache2`

### Create symbolic links for Apache2

```
cd /opt/ownrecipes/ownrecipes-apache2/
ln -s /opt/ownrecipes/ownrecipes-web/build /opt/ownrecipes/ownrecipes-apache2/public-ui
ln -s /opt/ownrecipes/ownrecipes-api/static-files /opt/ownrecipes/ownrecipes-apache2/static-files
ln -s /opt/ownrecipes/ownrecipes-api/site-media /opt/ownrecipes/ownrecipes-apache2/site-media
```

*OwnRecipes will create the* **site-media** *directory when you upload the first image.*

### Edit /etc/apache2/sites-available/default.conf


```
   <VirtualHost *:80>
       ServerName ownrecipes.domain.com
       ServerAdmin postmaster@domain.com
       Header always set Strict-Transport-Security "max-age=15768000"
       DocumentRoot "/opt/ownrecipes/ownrecipes-apache2/public-ui"
       <Directory "/opt/ownrecipes/ownrecipes-apache2/public-ui">
           # HANDLE 404 ERROR ON REFRESH
           RewriteEngine On
           RewriteBase /
           RewriteRule ^index\.html$ - [L]
           RewriteCond %{REQUEST_FILENAME} !-f
           RewriteCond %{REQUEST_FILENAME} !-d
           RewriteRule . /index.html [L]
           # HANDLE 404 ERROR ON REFRESH END
           Options Indexes FollowSymLinks MultiViews
           AllowOverride All
           Order allow,deny
           allow from all
           Require all granted
       </Directory>

       Alias /static /opt/ownrecipes/ownrecipes-apache2/public-ui/static
       <Location "/static/">
           Options Indexes FollowSymLinks MultiViews
           AllowOverride All
           Order allow,deny
           allow from all
           Require all granted
           AddOutputFilterByType DEFLATE text/plain
           AddOutputFilterByType DEFLATE text/javascript
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE application/xml
           AddOutputFilterByType DEFLATE application/xhtml+xml
       </Location>

       <Location "/api/">
           ProxyPass http://127.0.0.1:5210/api/
           ProxyPassReverse http://127.0.0.1:5210/api/
       </Location>

       <Location "/admin/">
           ProxyPass http://127.0.0.1:5210/admin/
           ProxyPassReverse http://127.0.0.1:5210/admin/
       </Location>

       Alias /static-files /opt/ownrecipes/ownrecipes-apache2/static-files
       <Directory "/opt/ownrecipes/ownrecipes-apache2/static-files">
           Options Indexes FollowSymLinks MultiViews
           AllowOverride All
           Order allow,deny
           allow from all
           Require all granted
       </Directory>
       Alias /site-media /opt/ownrecipes/ownrecipes-apache2/site-media
       <Directory "/opt/ownrecipes/ownrecipes-apache2/site-media">
           Options Indexes FollowSymLinks MultiViews
           AllowOverride All
           Order allow,deny
           allow from all
           Require all granted
       </Directory>
   </VirtualHost>
```

### Reload Apache configuration

`service apache2 restart`

Your OwnRecipes installation should run on your http://ownrecipes.domain.com.

It is HIGHLY recommended you eventually switch over to an HTTPS setup. Let's Encrypt is great for getting that set up.

## Install Web-Server: nginx (Option 2)

**Info: If you prefer Apache, stick to the first option above and skip this step.**

TODO

## Updating OwnRecipes

`cd /opt/ownrecipes/`

### Get the update from git sources

```
git clone https://github.com/ownrecipes/ownrecipes-api.git
git clone https://github.com/ownrecipes/ownrecipes-web.git
```

### Update the paths in your configuration files

* Update paths in prod-entrypoint.sh
* Update paths in gunicorn_start.sh

### Rebuild ownrecipes-web

* Rebuild ownrecipes-web with npm

### Restart services

```
systemctl restart ownrecipes.service
systemctl reload apache2.service
```
