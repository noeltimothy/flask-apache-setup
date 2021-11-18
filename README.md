# flask-apache-setup

# Petsmart Requirements

Petsmart as requested for the following workflows on BlueCat Gateway to support automation of their new store automation

## Workflow - New Store Setup

### Overview
This workflow handles the following
+ Provides the user an interface to add a new IP4Block for a new store
+ Provides the user with a set of networks as specified by the new-store network breakup
+ Provides the user with a set of DHCP scopes as specified by the new-store DHCP breakup
+ Provides the user with a set of options as specified by the new-store options 
+ Provides the user an interface to add a new IP4Network according to the old-net criteria

### UI
 
#### Screen 1

The user needs to enter the CIDR and select either a new-net or old-net format.
The screen automatically presents the user with the criteria for a new store.

```plantuml
@startuml
salt
{+
  .
  .
  New Store Block | . 
  " 10.0.0.0/24 " | .
  (X) newnet 
  () old-net
  ==
  .
  .
  The following networks will be created:
  ==
  {
  
  network       | DHCP Range  | Name | Actions
  .. | .. | .. | ..
  10.0.0.0/27   |  .4-23	  |	Mgmt//Vlan5 | merge/split/delete		
	10.0.0.32/27  |  .34-58		|Guest//Vlan10	| merge/split/delete	
	10.0.0.64/28  |	 .68-78		|PinPads//Vlan15		| merge/split/delete
	10.0.0.80/28  |  .82-92		|Wireless-WPA-Only//Vlan50		| merge/split/delete
	10.0.0.96/28  |  .98-108	| Wireless-WPA-Migration//Vlan55	| merge/split/delete
	10.0.0.112/28 |	 .114-124	| DMZ//Vlan25		| merge/split/delete
	10.0.0.128/27 |	 .130-156	| Voice//Vlan20		| merge/split/delete
	10.0.0.160/27 |	 .162-188	| Devices//Vlan40	| merge/split/delete
	10.0.0.192/29 |		na      | Wireless Phone Reg//Vlan35	| merge/split/delete
	10.0.0.208/28 |	 .210-220	| Omni//Vlan90		| merge/split/delete
	10.0.0.224/29	|	 .226-228	| Omni-Staging//Vlan80 | merge/split/delete
  }
  ==
  .
  .
  The following options will be added:
  ==
  {
    network | option | Option Value
    .. | .. | ..
    10.0.0.32/27 (Guest//Vlan10) | ^dns-server^ | 8.8.8.8, 4.4.4.4
    10.0.0.80/28 (Wireless-WPA-Only//Vlan50)	| ^domain-name^ | petstores.petsmart.com	
	  10.0.0.96/28 (Wireless-WPA-Migration//Vlan55) | ^domain-name^ | petstores.petsmart.com
	  10.0.0.208/28 (Omni//Vlan90) | ^domain-name^ | petstores.petsmart.com
	  10.0.0.224/29	(Staging//Vlan80)	| ^domain-name^ | petstores.petsmart.com
    10.0.0.128/27 (Voice//Vlan20) | ^tftp-server^ | x.x.x.x	
    10.0.0.208/28 (Omni//Vlan90) | ^dns-server^ | x.x.x.x, y.y.y.y
    .. | .. | ..
    [more options]
  }
  ==
  
  [Deploy]
}@enduml
```

PS:
+ The workflow will only allow the following limited set of options to be specified:
++ DNS Servers
++ Minimum Lease Time
++ Maximum Lease Time
++ Domain Name
++ TFTP Server

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

## Copy Basic Apache config for mycompany

```
<VirtualHost *:80>
		ServerName mywebsite.com
		ServerAdmin admin@mywebsite.com
		WSGIScriptAlias / /var/www/mycompany/mycompany.wsgi
		<Directory /var/www/mycompany/mycompany/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/mycompany/mycompany/static
		<Directory /var/www/mycompany/mycompany/static/>
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
sudo a2ensite mycompany
sudo systemctl reload apache2
```

## Setup WSGI 

```
cd /var/www/
mkdir -p mycompany/mycompany
sudo vi mycompany/mycompany.wsgi

----------------------------------------
#!/usr/bin/python3
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/mycompany/mycompany")

from mycompany import app as application
application.secret_key = 'Add your secret key'
----------------------------------------
:wq

sudo chmod -R www-data:www-data mycompany/
sudo chmod -R www-data:www-data mycompany/*
sudo service apache2 restart
```

## Basic Flask App

sudo vi /var/www/mycompany/mycompany/__init__.py
sudo vi /var/www/mycompany/mycompany/mycompany.py

```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def demo():
    return F"<h1> My company is under construction, please check back later <h1>", 200

if __name__ == '__main__':
    app.run()
```

