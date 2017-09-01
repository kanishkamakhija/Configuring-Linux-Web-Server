# Configuring-Linux-Web-Server
This project involves how to access, secure, and perform the initial configuration of a bare-bones Linux server. 
And also how to install and configure a web and database server and actually host a web application

# Project Overview
You will take a baseline installation of a Linux server and prepare it to host your web applications. 
You will secure your server from a number of attack vectors, install and configure a database server, 
and deploy one of your existing web applications onto it.

# Why this Project?
A deep understanding of exactly what your web applications are doing, 
how they are hosted, and the interactions between multiple systems are what define you as a Full Stack Web Developer. 
In this project, you’ll be responsible for turning a brand-new, bare bones, 
Linux server into the secure and efficient web application host your applications need.

# Create AWS instance
* create an account in aws here []
* Mark down the public Ip address from console of the instance created

# Getting into VM environment
* Launch vagrant machine by `vagrant up`
* Log in through ssh by `vagrant ssh`

# Upgrading and Updating the available package
* sudo apt-get update
* sudo apt-get upgrade

# Downloading Apache2, Git, Postgres, mod-wsgi

1. Apache2
* `sudo apt-get install apache2 libapache2-mod-wsgi git`
* Enable mod_wsgi: `sudo a2enmod wsgi`

3. Postgresql
* `sudo apt-get install libpq-dev python-dev`
* `sudo apt-get install postgresql postgresql-contrib`
* Check for prohibiting remote connections `sudo cat /etc/postgresql/9.3/main/pg_hba.conf`

# Installing Flask and dependencies
* `sudo apt-get install python-pip`
* `sudo pip install Flask`
* `sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils`
* `sudo pip install requests`

# Creating a new user
* `sudo adduser grader`

# Giving sudoers access to the grader
* `sudo nano /etc/sudoers.d/grader`
* Add this line: `grader ALL=(ALL) NOPASSWD:ALL` and save the file

# Logging in as a root user
* `ssh grader@x.x.x.x -p 2222 -i ~/.ssh/fileName`

# Generating encryption key pair
* Generate key on you local machine by:
* `ssh-keygen`
* Give the path name and then fileName as /path/fileName
* Other fields are optional.

# Installing public key
* `mkdir .ssh`
* `touch .ssh/authorized_keys`
* Get the ssh-key generated on localhost by copying the key from the file on your local machine: `cat .ssh/fileName.pub`
* Paste this copied ssh-key in the authorized_keys by : `nano /home/grader/.ssh/authorized_keys` and save the file in grader user.
* Add permissions to the .ssh directory by `chmod 700 .ssh` and to the authorized_keys file by `chmod 644 .ssh/authorized_keys`
* Restart the service :`sudo service ssh restart`

# Forcing key based authentication
* `sudo nano /etc/ssh/sshd_config`
* Make changes inside the file by making PasswordaAthentication no from yes and save the file
* Restart the service `sudo service ssh restart`

# Changing ssh port
* `sudo nano /etc/ssh/sshd_config`
* Search for the line of port no and change it from 22 2200
* `sudo service ssh restart`

# Disabling root login
* `sudo nano /etc/ssh/sshd_config`
*  Search for the line PermitRootUser and change it to no
* `sudo service ssh restart`

# Changing Firewall settigs
* `sudo ufw status`
* `sudo ufw default deny incoming`
* `sudo ufw default allow outgoing`

# Configuring ports in UFW
* `sudo ufw allow ssh`
* `sudo ufw allow 2200/tcp`
* `sudo ufw allow www`
* `sudo ufw allow ntp`
* To activate and enable Firewall on system startup `sudo ufw enable`

# Configure local timezone to UTC
* `sudo dpkg-reconfigure tzdata`
* For better synchronization of server's time : `sudo apt-get install ntp`

# Log in using ssh key generated
* Logout from the root and log in as grader user using
 `ssh -i <fileName>.pem grader@x.x.x.x -p 2200`

# Get into postgresql
* `sudo su - postgres`
* `psql`
* `CREATE USER <username> WITH PASSWORD '<password>'`; //Creating user
* `CREATE DATABASE <databaseName> WITH OWNER <username>;` //Creating databse
* `\c <databaseName>;` //Connecting to database
* `REVOKE ALL ON SCHEMA public FROM public;` //Revoking all rights from public
* `GRANT ALL ON SCHEMA public TO <username>;` //Granting all rights to user
* `EXIT;` //Exit from postgres and go back to grader

# Changing FLask application database connections
* engine = create_engine('postgresql://<databaseName>:<password>@localhost/<username>') , changes made in main .py file also.

# CLoning the Item-Catalog App
* `sudo mkdir /var/www/catalog`
* `sudo chown -R grader:grader /var/www/catalog`
* `git clone https://github.com/username/project_name.git catalog`
* `nano <filename.wsgi>` file and paste the following content into it:
    `import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0, "/var/www/catalog/")

    from <filename> import app as application`

# Editing the virtual file
* `sudo nano /etc/apache2/sites-available/000-default.conf`
    and add the following content:
  `<VirtualHost *:80>
    ServerName XX.XX.XX.XX
    ServerAdmin kanishkamakhija007@gmail.com
    WSGIScriptAlias / /var/www/catalog/<filename.wsgi>
    <Directory /var/www/catalog/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/static
    <Directory /var/www/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
 </VirtualHost>`
 * `sudo service apache2 restart`

# Creating database and adding dumps to it
* `python database_setup.py`
* `python lotsofmenu.py`    
