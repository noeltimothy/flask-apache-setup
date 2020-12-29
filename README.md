# flask-apache-setup

## Install Python3 and Apache with WSGI

```
sudo apt install python3-pip
sudo apt install python-dev
sudo apt install apache2 libapache2-mod-wsgi-py3
```

## Install Python modules as required

```
sudo pip3 install requests flask pandas
```

## Copy Basic Apache config for FlaskApp

```
<VirtualHost *:80>
		ServerName mywebsite.com
		ServerAdmin admin@mywebsite.com
		WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
		<Directory /var/www/FlaskApp/FlaskApp/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/FlaskApp/FlaskApp/static
		<Directory /var/www/FlaskApp/FlaskApp/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

## Enable FlaskApp on Apache

```
sudo a2ensite FlaskApp
sudo systemctl reload apache2
```

## Setup WSGI 

```
cd /var/www/
mkdir -p FlaskApp/FlaskApp
sudo vi FlaskApp.wsgi

----------------------------------------

#!/usr/bin/python3
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'

----------------------------------------

sudo service apache2 restart
```
