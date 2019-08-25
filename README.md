# Linux Server Configuration Project

## Introduction
It is an installation of a Linux server and  hosting  web applications onto it . It is a  secured  server from a number of attack vectors, installation and configuring a database server, and deploying  existing web applications onto it.
Host ip:http://52.24.125.52

## Required Software :
* Download and install [VirtualBox](https://www.virtualbox.org/wiki/Download_Old_Builds_5_2).
* Download and install [Vagrant](https://www.vagrantup.com/).

## Setup an environment :
* 1- Create a new folder on your computer , then open that folder within your terminal.
* 2-Type __vagrant init ubuntu/trusty64__ to tell Vagrant what kind of Linux virtual   machine you would like to run.
* 3-Type __vagrant up__ to download and start running the virtual machine
* 4- __vagrant ssh__ to connect to your virtual machine

## install new application :
* using __sudo apt-get install finger__  to use at working with users

## create new user :
* create new user using __sudo adduser grader__  with password grader.
* connect to it using __ssh grader@127.0.0.1 -p 2222_
## give sudo access To grader user :
* use __sudo ls /etc/sudoers.d__ to know users has sudo access
* then copy vagrant to new user using __sudo cp /etc/sudoers.d/vagrant /etc/sudoers.d/grader__
* edit new file (grader) using __sudo nano /etc/sudoers.d/grader__:
the edit is to replace vagrant by username (grader) at the file
* now you can check if  you have access by using __sudo cat /etc/passwd__
from grader account

## To force the user too reset its password nevt time:
 * Use  __sudo passwd -e grader__
## generate key to more authentication
* 1-__ssh-keygen__ this step will generate 2 file at the location you choose (.ssh/linuxCourse"or any other name you want")is the default one  
Note: you must generate it at your local machine not at your server to keep it private
* 2- open the .pub file and copy its content
* 3- make .ssh folder at server using __mkdir .ssh__
* 4-create file using __.ssh/authorized_keys__
* 5- open it to edit it using __nano .ssh/authorized_keys__
* 6-paste the content of your .pub file at it .then save and close
* 7- use __chmod 700 .ssh__
* 8- use __chmod 644 .ssh/authorized_keys__
* 9- Now you can log in to this user using __ssh grader@127.0.0.1 -p 2222 -i ~/.ssh/linuxCourse__

## Disable the password base login for vagrant:
* to do this you have to edit the configuration file for SSHD which is the service that is running on the server listening for all of your SSH connections" using __sudo nano /etc/ssh/sshd_config__ then restart the service using __sudo service ssh restart__


# firewall
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

* __sudo ufw allow 2200/tcp__
* __sudo ufw allow 80/tcp__
* __sudo ufw allow 123/udp__
* __sudo ufw enable__

## Configure the local timezone to UTC
Configure the time zone __sudo dpkg-reconfigure tzdata__


## Install and configure Apache to serve a Python mod_wsgi application
* 1-Install Apache __sudo apt-get install apache2__
* 2-Install mod_wsgi __sudo apt-get install python-setuptools libapache2-mod-wsgi__
* 3-Restart Apache __sudo service apache2 restart__
* 4-Install PostgreSQL __sudo apt-get install postgresql__
* 5-Login as user "postgres" __sudo su - postgres__
* 6-Get into postgreSQL shell __psql__
* 7-Create a new database named catalog and create a new user named catalog in postgreSQL shell
  - postgres=# __CREATE DATABASE catalog;__
  - postgres=# __CREATE USER catalog;__
* 8-Set a password for user catalog
postgres=# __ALTER ROLE catalog WITH PASSWORD 'password';__
* 9-Give user "catalog" permission to "catalog" application database
postgres=# __GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;__
* 10-Quit postgreSQL postgres=# __\q__
* 11-Exit from user "postgres"
__exit__
## clone and Deploy catalog project:
* 1-Install git using __sudo apt-get install git__
* 2-Use __cd /var/www__ to move to the /var/www directory
* 3-Create the application directory __sudo mkdir FlaskApp__
* 4-Move inside this directory using __cd FlaskApp__
* 5-Clone the Catalog App to the virtual machine git clone https://github.com/mariammira/Category.git
* 6-Rename the project's name __sudo mv ./Category ./FlaskApp__
* 7-Move to the inner FlaskApp directory using __cd FlaskApp__
* 8-Rename website.py to __init__.py using __sudo mv project.py __init__.py__
* 9-Edit database.py, project.py and functions_helper.py and change engine = create_engine('sqlite:///Categories.db') to engine = create_engine('postgresql://catalog:password@localhost/catalog')
* 10-Install pip __sudo apt-get install python-pip__
* 11--Use pip to install dependencies __sudo pip install -r requirements.txt__
* 12-Install psycopg2 __sudo apt-get -qqy install postgresql python-psycopg2__
* 13-Create database schema __sudo python database.py__
* 14-Create FlaskApp.conf to edit: __sudo nano /etc/apache2/sites-available/FlaskApp.conf__

Add the following lines of code to the file to configure the virtual host.

__<VirtualHost *:80>
	ServerName 52.24.125.52
	ServerAdmin mariamosamamira@gmail.com
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
</VirtualHost>__
* 15-Enable the virtual host with the following command: sudo a2ensite FlaskApp

* 16-Create the .wsgi File under /var/www/FlaskApp using :

- __cd /var/www/FlaskApp__
- __sudo nano flaskapp.wsgi__
Add the following lines of code to the flaskapp.wsgi file:

__import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'__ will send it with project but not mentioned here
* 17-Restart Apache using  __sudo service apache2 restart__
* 18- __cd /var/www/FlaskApp/FlaskApp__
* 19-__sudo python __init__.psycopg2__
* 20- visit 52.24.125.52 at your favorite browser  
## References:
* https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
* https://github.com/kongling893/Linux-Server-Configuration-UDACITY
