# Linux-Server-Configuration-Udacity
Udacity Nanodegree Program - Full Stack Web Developer
# **Project Introduction**

These are the steps to install a Linux Server and prepared it for Web applications.

**Server Details**
- **IP address** : 18.196.167.64
- **SSH login username:** grader
- **SSH port :** 2200
- **URL :** http://3.85.137.110/

# Configuration Steps

**1. Create an instance in AWS lightsail**

- Go to[ AWS Lightsail](https://lightsail.aws.amazon.com/ " AWS Lightsail") and create a new account.
- Click Create instance and choose `Linux/Unix`, `OS only`, `Ubuntu 18.04LTS`
- Select month payment
- Click on `Create` button


**2. Set up SSH key**

On your local machine create a public and private key pair.
Run the next command:
`$ ssh-keygen`
Follow the instructions and the you can get 2 files **id_rsa** and** id_rsa.pub**
> **Note: **I ran the command using Git-bash for windows. The files where stored in C:\Users\account_name\\.ssh


**3. Change SSH port**

Open the following file:
    `nano /etc/ssh/sshd_config`
Modify` #Port 22` for `Port 2200`
Restart SSH service: `service ssh restart`


**4. Configure Timezone to Use UTC**

Run the command:
`# sudo dpkg-reconfigure tzdata`
Then select `None of the Above` and then choose `UTC`

**5. Setup Uncomplicated Firewall  (UFW)**

Do the following commands:
```
sudo ufw status
sudo ufw default deny incoming
sudo ufw default deny outgoing
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw deny 22
sudo ufw enable
sudo ufw status
```
Also on the AWS page go to networking tab add `Application Custom Protocol UPD Port 123` and `Application Custom Protocol TCP Port 2200`

**6. Setup `grader` user**

Run the command:
`su - grader`

The following command add the grader user to sudo to grant admin access.
`usermod -aG sudo grader`

Now enter the following commands:
```
mkdir .ssh
chmod 700 .ssh
cd .ssh/
touch authorized_keys
chmod 644 authorized_keys
```
After the previews steps , go back to your local machine and copy the content of the public key file ~/.ssh/id_rsa.pub.  and paste the public key to the server's authorized_keys file using nano

Then you can access using :
`ssh grader@3.85.137.110 -p 2200`

**7. Update Server**

```
apt-get update 
apt-get upgrade
reboot
```
**8. Install Apache Web Server**

Using the grader user do:
```
sudo apt update
sudo apt install apache2
```
To confirm go to http://3.85.137.110/ and check if the apache default page shows up.

**9. Install pip**

The project is created on Python2.7
Run the following command:
`sudo apt install python-pip`

**10. Install Postgress**
Run the following commands:

```
sudo apt-get install libpq-dev python-dev
sudo apt-get install postgresql postgresql-contrib
sudo su - postgres
psql
CREATE USER catalog WITH PASSWORD 'password';
ALTER USER catalog CREATEDB;
CREATE DATABASE catalog WITH OWNER catalog;
GRANT ALL ON SCHEMA public TO catalog;
\q
exit
```
**11. Install libraries**

```
sudo pip install --upgrade Flask 
sudo pip install --upgrade SQLAlchemy  
sudo pip install --upgrade httplib2  
sudo pip install --upgrade oauth2client  
sudo pip install --upgrade requests   
sudo pip install --upgrade psycopg2   
sudo pip install --upgrade psycopg2-binary

```

**12. Install Git and clone the project and update code**

For ubuntu 18.04lts git is preinstalled, but this is command if you are using a different version:

`sudo apt-get install git`

Then clone the project:
```
cd /var/www/
sudo mkdir FlaskApp
cd FlaskApp/
sudo git clone https://github.com/ownleviathan/Catalog_Item-Sports-Udacity.git FlaskApp
cd FlaskApp/
```
Change the `project.py` to `__init__.py`

`mv project.py to __init__.py`

Finally, change the engine command inside the Flask application:

`engine = create_engine('postgresql://catalog:password@localhost/catalog')`

Setup the database on postgress

`python /var/www/FlaskApp/FlaskApp/database_setup.py`


**13. Setup Apache and Run Flask**

Configure the virtual hosts:
`sudo nano /etc/apache2/sites-available/FlaskApp.conf`

Add the following lines on it:
```
   ServerName 3.85.137.110
   ServerAdmin youremail@domain.com
   WSGIScriptAlias / /var/www/FlaskApp/FlaskApp/flaskapp.wsgi
   <Directory /var/www/FlaskApp/FlaskApp/>
       Require all granted
   </Directory>
   Alias /static /var/www/FlaskApp/FlaskApp/static
   <Directory /var/www/FlaskApp/FlaskApp/static/>
       Require all granted
   </Directory>
   ErrorLog ${APACHE_LOG_DIR}/error.log
   LogLevel warn
   CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

Enable the virtual host and restart apache

```
sudo a2ensite FlaskApp
sudo service apache2 restart
```

Create the .wsgi File

```
cd /var/www/FlaskApp/FlaskApp/
sudo nano flaskapp.wsgi
```
Add the following lines to that file:

```
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/FlaskApp/FlaskApp/")

from FlaskApp import app as application
```
Restart Apache Server
`sudo service apache2 restart`



**14. Check website and errors**
Go to http://3.85.137.110/ and you should check the website

if you are getting and errors, check the logs file:

`sudo cat /var/log/apache2/error.log`

# References
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
