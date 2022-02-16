*Please note, most every line that contains commands will require either running as root or sudo*

## Install Dependencies

`sudo apt install git build-essential`

## Create Base Directory

*It is recommended to stick to the path "/opt/ownrecipes/ownrecipes-api" to minimize the required changes in the configuration files.*

```
mkdir /opt/ownrecipes/
```

## Database

We will use MariaDB.

### Install

```
sudo apt install mariadb-server
```
Don't forget to run `sudo mysql_secure_installation`!

Install Additional Dependencies:

`sudo apt install default-libmysqlclient-dev libssl-dev`

### Setup Database

Log Into Your MySQL Database:

`sudo mysql`

Create Database and Db-User:

`CREATE DATABASE ownrecipes;`

*Change username and password. You will have to change that in the configuration files, too.*

*It is recommended to stick to the username "ownrecipes" to minimize the required changes in the configuration files.*

`GRANT ALL PRIVILEGES ON ownrecipes.* TO 'username'@'localhost' IDENTIFIED BY 'password';`

Example:

`GRANT ALL PRIVILEGES ON ownrecipes.* TO 'ownrecipes'@'localhost' IDENTIFIED BY 'db-trustNo1';`

Commit Changes:

`FLUSH PRIVILEGES;`

Exit From MySQL:

`EXIT;`

## ownrecipes-api

### Install dependencies

`sudo apt-get install python3 python3-pip git build-essential`

### Setup the files

**Get the Source of ownrecipes-api from GitHub:**

`cd /opt/ownrecipes`

```
git clone https://github.com/ownrecipes/ownrecipes-api.git
cd /opt/ownrecipes/ownrecipes-api
```

**Create .env Environment file:**

First, copy one of the following files for your setup (locahost, server or proxy).
Then, open the file `/opt/ownrecipes/ownrecipes-api/.env` and change the [variables](docs/Setting_up_env_file.md) to your needs. You will have to change at least all variables with "\<placeholder-values\>".

*localhost:*

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/localhost/.env /opt/ownrecipes/ownrecipes-api/`

*server:*

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/server/.env /opt/ownrecipes/ownrecipes-api/`

*proxy:*

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/proxy/.env /opt/ownrecipes/ownrecipes-api/`

**Copy the /opt/ownrecipes/ownrecipes-api/base/prod-entrypoint.sh file:**

*If you are using a different directory than "/opt/ownrecipes", then you have to adjust the file to your needs.*

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/prod-entrypoint.sh /opt/ownrecipes/ownrecipes-api/base/`

**Copy the /opt/ownrecipes/ownrecipes-api/base/gunicorn_start.sh file:**

*If you are using a different directory than "/opt/ownrecipes", or can't use the os user "ownrecipes", then you have to adjust the file to your needs.*

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/gunicorn_start.sh /opt/ownrecipes/ownrecipes-api/base/`

**Create systemd service to run the api:**

*If you are using a different directory than "/opt/ownrecipes", or can't use the os user "ownrecipes", then you have to adjust the file to your needs.*

`cp /opt/ownrecipes/ownrecipes-api/samples/no_docker/ownrecipes.service /lib/systemd/system/`

### Create the OS user

```
sudo useradd -m -d /opt/ownrecipes username
sudo chown -R username:groupname /opt/ownrecipes
```

Example:

```
sudo useradd -m -d /opt/ownrecipes ownrecipes
sudo chown -R ownrecipes:ownrecipes /opt/ownrecipes
```

Tip: Change default shell to bash for more convenient terminal usage:

`sudo chsh -s /bin/bash ownrecipes`

### Install the Python Requirements

First, change user to the OS user for ownrecipes

```
sudo su ownrecipes
```

```
pip3 install -U -r /opt/ownrecipes/ownrecipes-api/base/requirements.txt
```

On some systems, it may be neccessary to add the newly installed tools to the PATH.
Check that the python tools are working by executing:

```
gunicorn --version
```

If you see an error, add the python tools permanently to the path. To permanently add shell path:

```
echo "export PATH=\$PATH:~/.local/bin" >> ~/.bashrc
echo "export PATH=$PATH:~/.local/bin" >> /opt/ownrecipes/ownrecipes-api/.env
```

Restart the terminal session and re-validate that gunicorn is found.

### Populate the database

*This step is optional.*

First, change user to the OS user for ownrecipes

```
sudo su ownrecipes
```

```
cd /opt/ownrecipes/ownrecipes-api
/bin/bash -ac '. .env; exec python3 manage.py makemigrations'
/bin/bash -ac '. .env; exec python3 manage.py migrate'
/bin/bash -ac '. .env; exec python3 manage.py createsuperuser'

/bin/bash -ac '. .env; exec python3 manage.py loaddata course_data.json'
/bin/bash -ac '. .env; exec python3 manage.py loaddata cuisine_data.json'
/bin/bash -ac '. .env; exec python3 manage.py loaddata news_data.json'
/bin/bash -ac '. .env; exec python3 manage.py loaddata recipe_data.json'
/bin/bash -ac '. .env; exec python3 manage.py loaddata ing_data.json'
```

### Start the api

Open a new terminal.

`sudo service ownrecipes start`

**Check if the api running correctly:**

`sudo service ownrecipes status`

If all is running correctly, you should see the line
```diff
+ Active: active (running)
```

... and probably as last lines `[INFO] Booting worker with pid: ...`.

### Troubleshooting

#### Run the api in a terminal

In case your api just won't work as expected, you could run your api in a terminal to hopefully get some more detailed error information.

Log in as ownrecipes user:

```
sudo su ownrecipes
cd ~/ownrecipes-api
```

Make the entrypoint-script executable:

```
chmod +x base/prod-entrypoint.sh
```

Run the api:

*The following command will source the environment file and make the variables available to all subsequent called scripts.*

`/bin/bash -ac '. .env; exec ./base/prod-entrypoint.sh'`


<hr />


## ownrecipes-web

### Install dependencies

**Nodejs:**

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

### Setup the files

**Get the source of ownrecipes-web from git:**

`cd /opt/ownrecipes/`

```
git clone https://github.com/ownrecipes/ownrecipes-web.git
cd /opt/ownrecipes/ownrecipes-web/
```

**Create environment file in /opt/ownrecipes-web/**

`touch /opt/ownrecipes/ownrecipes-web/.env.local`

Edit the created files .env.local and insert the following data in it.

*Read the comments carefully and change the necessary parts to your configuration.*

*In this example, our api will still listen on port **5210**.*

```
# url to your backend
export REACT_APP_API_URL=https://ownrecipes.domain.com:5210
# url to the django admin site
export REACT_APP_ADMIN_URL=https://ownrecipes.domain.com:5210/admin
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


<hr />


## Web-Server: Apache 2 (Option 1)

**Info: If you prefer nginx, stick to the second option below and skip this step.**

`sudo apt-get install apache2`

### Create symbolic links for Apache2

```
mkdir /opt/ownrecipes/ownrecipes-apache2/
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
           RewriteRulesystemctl ^index\.html$ - [L]
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

## Web-Server: nginx (Option 2)

**Info: If you prefer Apache, stick to the first option above and skip this step.**

TODO


<hr />


## Updating OwnRecipes

`cd /opt/ownrecipes/`

**Get the update from git sources:**

```
git clone https://github.com/ownrecipes/ownrecipes-api.git
git clone https://github.com/ownrecipes/ownrecipes-web.git
```

**Update the paths in your configuration files:**

* Update paths in prod-entrypoint.sh
* Update paths in gunicorn_start.sh

**Rebuild ownrecipes-web:**

* Rebuild ownrecipes-web with npm

**Restart services:**

```
sudo service ownrecipes restart
sudo service apache2 reload
```
