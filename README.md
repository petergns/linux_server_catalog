Linix Server Catalog Project
====================
Project files for the Linix Server Catalog in the Udacity Full Stack Nanodegree.

This project contains a database of comic book universe characters, and shows how to host it on a Ubuntu Linix-based Amazon AWS Lightsail instance.

## Set Up Instructions

Follow these instructions to set up the Linix Server Catalog for the Linix Server Catalog Project.

### Required Starting Resources:
* Web Browser such as Chrome, Firefox, or Safari.
* [Amazon AWS Lightsail Ubuntu Linix Instance](https://lightsail.aws.amazon.com/ls/docs/getting-started/article/getting-started-with-amazon-lightsail).
* [Git](https://git-scm.com/downloads).
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads).
* [Vagrant](https://www.vagrantup.com/).
* [Ubuntu Linix Virtual Machine](https://github.com/udacity/fullstack-nanodegree-vm).
* [Optional - Github Desktop](https://desktop.github.com/). 

**AWS Instance Note:** It might be required to clear your browser cookies or use more than one browser, if there is a problem of constant logouts under a minute.

### Setup and Configure Instance

Create an Amazon AWS Lightsail account. [1]

Create a Ubuntu Linix-based instance on Lightsail. [2]

1. Create new instance i.e. my_instance_name on Lightsail.
2. Create and attach a static ip for your Lightsail instance i.e. my_static_ip.
3. Note the Public and Private IPs for your instance, and add the following ports:

Static/Public IP: `18.216.39.42`
Private IP - Example: 127.00.0.01

| Application   | Protocol      | Port Range  |
| ------------- |:-------------:| -----------:|
| SSH           | TCP           | 22          |
| HTTP          | TCP           | 80          |
| CUSTOM        | TCP           | 123         |
| CUSTOM        | TCP           | 2200        |
| CUSTOM        | TCP           | 5000        |
| CUSTOM        | TCP           | 8000        |

**Note On Ports:** TCP 8000 is Optional, but might be useful for flexibility. 

Find the DNS Address of Instance

There are several sites that allow reverse IP lookup to allow you to do this. [3]

In the case of this instance it is: `http://ec2-18-216-39-42.us-east-2.compute.amazonaws.com`

### Update the instance

1. Login to the instance through SSH on your browser.
2. Update the update list: `sudo apt-get update`
3. Upgrade the distribution: `sudo apt-get dist-upgrade`
When prompted select to install 'Distribution Maintainers version'.
4. Update the update list again and apply upgrades:
```
sudo apt-get update
sudo apt-get upgrade
```
5. Remove no longer required packages: `sudo apt autoremove snap-confine`
6. Install Unattended Upgrades with: `sudo apt-get install unattended-upgrades`
7. Reconfigure the Unattended Upgrades with: `sudo dpkg-reconfigure -plow unattended-upgrades`
8. Reboot the instance with: `sudo reboot`
9. Restart the instance through SSH.

### Create user accounts

1. Enter Root: `sudo su`
2. Set ubuntu password using: `passwd ubuntu`
You will be asked to set a new UNIX password.
3. Exit Root with exit command: `exit`
4. Enter ubuntu account as super user: `sudo su - ubuntu`
5. Create grader user and set UNIX Password:
`sudo adduser grader`
When prompted for Full Name add: `Udacity Grader` or `Grader`
6. Create grader directory with: `sudo useradd -m -s /bin/bash grader`
7. Make grader a super user with: `usermod -aG sudo username`
8. Enter grader account as super user: `sudo su - grader`
9. Create catalog user and set UNIX Password: `sudo adduser catalog`
When prompted for Full Name add: `Database Catalog` or `Catalog`

**User Account Note:** You may be prompted to enter this password, so it is best to note these down.

### Set IPs and Ports

**Editing Note:** If any of these files are blank, you might not be in the file, if so use:
`cd /etc/` and `sudo nano hosts`
1. Make sure you are logged in as the super user grader: `sudo su - grader`
2. Enter hosts file: `sudo nano /etc/hosts`
Underneath the existing IP write: `STATIC_IP_HERE ubuntu`
Save with ctrl+x and y on prompt (or as directed by nano editor)
3. Add Port 2220 to sshd_config file: `sudo nano /etc/sshd_config`
4. Underneath the existing Port 22 (or SSH port) write: `Port 2200`
5. Edit PermitRootLogin prohibit-password to: `PermitRootLogin no`
6. Edit PasswordAuthentication no to: `PasswordAuthentication yes`
Save with ctrl+x and y on prompt (or as directed by nano editor)

**Note on Ports:** If you disable Port 22 on Lightsail, you will be unable to login on the Ligthsail web page.
**Note on PermitRootLogin:** Root Login is best kept disabled for security reasons, and only enabled when you need to use it.

### Create Instance Snapshot/Backup

**Note on Snapshots:** It is useful to have a snapshot just in case anything goes wrong while using your instance, even if it might take a few minutes to create. You can always delete and add instances as you go.

1. Exit your instance using: `exit`
2. On the Lightsail web page, click on your instance, and add a snapshot.
3. Wait for it to complete creating your snapshot.

Configure Firewall for your Instance

1. Enter grader account as super user: `sudo su - grader`
2. Set Firewall with:
```
sudo default ufw deny incoming
sudo default ufw allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/tcp
sudo ufw allow www
sudo ufw allow ssh
```
3. Start Firewall and Check Status with:
```
sudo ufw enable
sudo ufw status
sudo service ssh restart
```
Create SSH Key Pairs
------
**SSH Note:** You can generate new keys, but it is best keeping track of them. Once you have created your SSH Key Pair, you can select and copy to a text editor such as sublime text or notepad++ for later use. Lighsail instances through the browser have a paste window, that you can paste from. 

1. Login as user grader: `sudo su - grader`
2. Create SSH Key pair with: `ssh-keygen -t rsa`
When prompted for where to place type: grader
3. While still logged in as grader do the following:
```
sudo mkdir /home/grader/.ssh
sudo chown grader:grader /home/grader/.ssh
sudo chmod 70 /home/grader/ssh
sudo chown grader:grader /home/grader/.ssh/authorized_keys
sudo chmod 644 /home/grader/.ssh/authorized_keys
```
This will apply permissions to the ssh folder and authorized keys to the user grader.

You can now login with:
ssh -i /.ssh/id_rsa grader@YOUR_LIGHTSAIL_IP_HERE -p 2200

Set Timezone to UTC and Install NTP
------
1. Login as user grader: `sudo su - grader`
2. Set Timezone to UTC: `sudo timedatectl set-timezone UTC`
3. Install NTP: `sudo apt-get install ntp`
4. Reboot the Instance with: `sudo reboot`

Install Major Packages and Clone Application
------
### Install APACHE2

1. SSH into the instance.
2. Login in as user grader (if not logged in already): `sudo su - grader`
3. Install Apache2 for the instance with: `sudo apt-get install apache2`
4. Check that Apache2 is loaded at the Static/Public IP, on your browser:
`http://18.216.39.42`
If so continue to the next step.
5. Install mod-wsgi to allow you to run wsgi files:
`sudo apt-get install libapache2-mod-wsgi`
6. Go to the sites-enabled directory in apache2:
```
cd /etc/apache2/sites-enabled/
ls
```
7. Create and edit the 000-default.conf file: `sudo nano 000-default.conf`
8. Add in the following line before </VirtualHost>:
`WSGIScriptAlias / /var/www/html/myapp.wsgi`
9. Restart Apache: `sudo apache2ctl restart`

### Install PostgreSQL and Create Catalog User

1. Install PostgreSQL:
`sudo apt-get install postgresql postgresql-contrib`
2. Login as postgres user: `sudo su - postgres`
3. Create catalog user for PostgreSQL: `createuser --interactive catalog`
When prompted by dialog set:
```
n for super user
y for creating databases and other prompts
db-password for password
```
4. Exit postgres account with:
`exit`

### Install and Configure Git 

**Git Note:** It might be a good idea to use the same username and email that you use with github.

1. Login to grader user with: `sudo su - grader`
2. Check that Git is installed with: `sudo apt-get install git`
3. Set global username and email:
`sudo git config -global user.name "your_username`
`sudo git config -global user.email "your_email@emailhost.com"`

### Clone Application to Instance

1. Login to grader user (if not logged in already): `sudo su - grader`
3. Move to www directory: `cd var/www/`
4. Clone repository into instance:
`sudo git clone https://github.com/petergns/linix_server_catalog.git catalog`

Install Additional Packages
------
**Required Packages Note:** Depending on your instance you might have to install more than on this list.

1. Login to grader user (if not logged in already): `sudo su - grader`
2. Install the following packages with:
```
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqalchemy python-pip
sudo apt-get install python-dev

sudo pip install sqlalchemy
sudo pip install python-psycopg2
sudo pip install Flask-SQLAlchemy
sudo pip install oauth2client
sudo pip install --upgrade oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install flask-seasurf
```
Configure Application on Instance
------

### Create Catalog Configuration File

**Catalog Configuration Note:** Normally you would have to create a .conf file from scratch i.e:
`sudo nano /etc/apache2/sites-available/catalog.conf`
1. Login to grader user (if not logged in already) with: `sudo su - grader`
2. Move to catalog/temp/ directory: `cd /var/www/catalog/temp`
3. Move catalog.conf file to sites-available:
`sudo mv catalog.conf /etc/apache2/sites-available/`
4. Delete temp directory: `sudo rm -rf temp`
5. Check .conf file is in directory:
```
cd /etc/apache2/sites-available/
ls
```
6. Edit configuration file email: `sudo nano catalog.conf`
7. Add your email address in place of youremail@youremailprovider.com.
8. Disable default configuration with: `sudo a2dissite 000-default.conf`
9. Enable catalog configuration file with: `sudo a2ensite catalog.conf`
10. Restart Apache with: `sudo service apache2 restart`

###  Create Virtual Environment

1. Login to grader user (if not logged in already) with: `sudo su - grader`
2. Move to /var/www/catalog/catalog with:
`cd var/www/catalog/catalog`
3. Install virtual environment and apply permissions:
```
sudo pip install virtualenv
sudo virtualenv venv
sudo chmod -R 777 venv
```
4. Activate Virtual Environment:
`source venv/bin/activate`
5. Install these packages in Virtual Environment:
```
pip install Flask
pip install httplib2
pip install requests
pip install --upgrade oauth2client
pip install sqlalchemy
pip install Flask-SQLAlchemy
pip install python-psycopg2
```
5. Run python testing file:
`python __init__.py`
If this shows it is connected, then it is complete.
6. Deactivate the virtual environment:
`deactivate`

###  Create PostgreSQL Database

1. Login to postgresql user with: `sudo su - postgresql`
2. Move to the var/www/catalog/catalog directory:
`cd var/www/catalog/catalog`
3. Enter psql with: `psql`
4. Create user catalog in psql and verify it exists:
```
CREATE USER catalog WITH PASSWORD 'db-password';
\du
```
5. Create database with owner as catalog:
`CREATE DATABASE catalog WITH OWNER catalog`
6. Connect to database with: `\c catalog`
7. Revoke public schema and give access to catalog role:
```
REVOKE ALL ON SCHEMA public FROM public;
GRANT ALL ON SCHEMA public TO catalog;
```
8. Quit psql with: `\q`
9. Exit postgres user with: `exit`

### Configure and Create .htaccess File

**.htaccess file Note:** This file is used to control access of the application to the web.

1. Login to grader user (if not logged in already) with: `sudo su - grader`
2. Move to the var/www/catalog/ directory: `cd var/www/catalog`
3. Create an .htaccess file to make the github repository inaccessible:
```
sudo nano .htaccess
RedirectMatch 404 /\.git
```
4. Move to the var/www/catalog/catalog directory: `cd catalog`
5. Replace the testing __init__.py with the application __init__2.py:
`sudo cp __init__2.py __init__.py`
6. Check that the file has changed with: `sudo nano __init__.py`
7. Login to postgres user with: `sudo su - postgres`
8. Run the database_setup.py and then universe_characters.py files:
```
python database_setup.py
python universe_characters.py
```
9. There should be a print to confirm that characters have been added.
10. Exit postgres user with: `exit`
11. Restart Apache2 with: `sudo service apache2 restart`
12. Exit your instance.

## Login Authorization Configuration

**Login Authorization Note:** You need to have a [facebook developer account](https://developers.facebook.com/) and a [google developer account](https://console.developers.google.com/).

1. Login to your google developer account, go into your API credentals for your app.
2. For Authorized JavaScript orgins include:
```
Your Static IP ie. http://18.216.39.42
Your DNS Address ie. http://ec2-18-216-39-42.us-east-2.compute.amazonaws.com
http://localhost:5000
http://127.0.0.1:5000
```
3. For Authorized redirect URIs include:
```
http://ec2-18-216-39-42.us-east-2.compute.amazonaws.com/oauth2callback
http://ec2-18-216-39-42.us-east-2.compute.amazonaws.com/login
http://ec2-18-216-39-42.us-east-2.compute.amazonaws.com/gconnect
http://localhost:5000/login
http://localhost:5000/gconnect
```
4. Login to your facebook developer account, and edit the app in my apps:
```
http://localhost:5000/login/callback
http://18.216.39.42/login/callback
http://ec2-18-216-39-42.us-east-2.compute.amazonaws.com/callback
```
5. SSH into your Lightsail Instance.
6. Login to grader user (if not logged in already) with: `sudo su - grader`
7. Edit the client_secrets.json file and make sure the URIs match:
`sudo nano /var/www/catalog/client_secrets.json`
8. Restart apache2: `sudo service apache2 restart`

**Adding URIs note:** Not all these are required, but are useful to have if you want to have a locally hosted version.

Check Web Application

Your web application should be functioning.

1. Go to your DNS address on your browser ie: http://ec2-18-216-39-42.us-east-2.compute.amazonaws.com
2. Login with your Google Account.
3. Login with your Facebook Account.
4. View catalog data.

### Useful Editors:
<ul>
  <li>Atom(https://atom.io/)
  <li>Git Nano(https://www.nano-editor.org/)
  <li>Notepad++(https://notepad-plus-plus.org/)
  <li>Sublime Text(https://www.sublimetext.com/)
</ul>

### Catalog When Logged In
![Image of Output](https://github.com/petergns/item-catalog-project/blob/master/comic-universe-catalog.PNG)

### Adding a Comic Book Character Page
![Image of Output](https://github.com/petergns/item-catalog-project/blob/master/add-new-character.PNG)

## Author
[petergns](https://github.com/petergns)

### Free Image Resource
1. https://pixabay.com/en/x-men-hero-marvel-comic-book-2640250/

## Acknowledgments
Acknowledgments to [Udacity](https://www.udacity.com/) and [Amazon AWS Ligthsail](https://aws.amazon.com/lightsail/) for the resources that helped me develop this.

## Sources
Create an Amazon AWS Lightsail account
[1]:https://portal.aws.amazon.com/

Create a Ubuntu Linix-based instance on Lightsail
[2]:https://lightsail.aws.amazon.com/ls/docs/getting-started/article/getting-started-with-amazon-lightsail

Mxtoolbox.com IP Lookup to Find DNS
[3]:https://mxtoolbox.com/SuperTool.aspx

Must copy and rename file
[4]:https://askubuntu.com/questions/460206/must-copy-and-rename-file
```
Copy and rename in the same time (also change filename, not only path):
cp program3.cpp homework6.cpp
Rename only:
mv program3.cpp homework6.cpp
```

How to List Databases and Tables in PostgreSQL Using psql
[5]:https://chartio.com/resources/tutorials/how-to-list-databases-and-tables-in-postgresql-using-psql/ 
```
postgres=# \c sales
You are now connected to database "sales" as user "ubuntu".
sales=#
```

500 Internal Server Error - How can I fix this
[6]:https://www.digitalocean.com/community/questions/500-internal-server-error-how-can-i-fix-this-this-website-was-supposed-to-be-a-christmas-present
```
sudo tail /var/log/apache2/error.log
```

Amazon Lightsail: How to set up your first instance
[7]: https://cloudacademy.com/blog/how-to-set-up-your-first-amazon-lightsail/

How do I remove a full directory in Linux?
[8]: https://www.computerhope.com/issues/ch000798.htm

Generating a new SSH key and adding it to the ssh-agent'
[9]: https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/

Download and set up PuTTY to connect using SSH in Amazon Lightsail
[10]: https://lightsail.aws.amazon.com/ls/docs/how-to/article/lightsail-how-to-set-up-putty-to-connect-using-ssh

How to manage PostgreSQL databases and users from the command line
[11]:https://www.a2hosting.com/kb/developer-corner/postgresql/managing-postgresql-databases-and-users-from-the-command-line

PostgreSQL Server Installation and Configuration
[12]: http://openobject-documentation.readthedocs.io/en/latest/1/linux/postgres/index.html

postgresql database owner can't access database - “No relations found.”
[13]: https://stackoverflow.com/questions/7758533/postgresql-database-owner-cant-access-database-no-relations-found/7758860#7758860

PostgreSQL 10.1 Documentation
[14]: https://www.postgresql.org/docs/10/static/index.html

Start / Stop and Restart Apache 2 Web Server Command
[15]: https://www.cyberciti.biz/faq/star-stop-restart-apache2-webserver/

How to Move, Copy, and Delete Files in Linux
[16]: http://www.hostingadvice.com/how-to/move-copy-delete-files-linux/

Psycopg 2.7.4.dev1 documentation usage
[17]: http://initd.org/psycopg/docs/usage.html

mod_wsgi (Apache)
[18]: http://flask.pocoo.org/docs/0.10/deploying/mod_wsgi/

[SOLVED] Configuring Linux: Google OAuth invalid request
[19]: https://discussions.udacity.com/t/solved-configuring-linux-google-oauth-invalid-request/376259

sqlalchemy.exc.OperationalError: (OperationalError) unable to open database file None None
[20]: https://stackoverflow.com/questions/18208492/sqlalchemy-exc-operationalerror-operationalerror-unable-to-open-database-file

Creating user, database and adding access on PostgreSQL
[21]: https://medium.com/coding-blocks/creating-user-database-and-adding-access-on-postgresql-8bfcd2f4a91e

Create and Drop Roles in PostgreSQL
[22]: https://support.rackspace.com/how-to/postgresql-creating-and-dropping-roles/

PostgreSQL 9.5.10 Documentation createdb
[23]: https://www.postgresql.org/docs/9.5/static/app-createdb.html

SQLAlchemy 1.2 Documentation Error Messages
[24]: http://docs.sqlalchemy.org/en/latest/errors.html#error-e3q8

Virtual Environments
[25]: http://modwsgi.readthedocs.io/en/develop/user-guides/virtual-environments.html
