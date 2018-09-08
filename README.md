# Linux-Server-Configuration Project of Udacity Full Stack Web Developer-II Nanodegree.

# Project Description
In this project there is a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

# Technical Information About the Project

- **Server IP Address:** 167.99.170.26
- **Server Alias:** 167.99.170.26.xip.io
- **SSH server access port:** 2200
- **SSH login username:** grader
- **Application URL:** http://167.99.170.26.xip.io

# Steps to Set up the Server

## 1. Creating the RSA Key Pair

On your local machine, you will first have to set up the public and private key pair. This key pair will be used to authenticate yourself while securely logging in to the server via SSH. The private key will be kept with you in your local machine, and the public key will be stored in the server.

To generate a key pair, run the following command:

   ```console
   $ ssh-keygen
   ```

When it asks to enter a passphrase, you must leave it empty. A passphrase adds an additional layer of security to prevent unauthorized users from logging in.

You now have a public and private key that you can use to authenticate. The public key is called `id_rsa.pub` and the corresponding private key is called `id_rsa`. The key pair is stored inside the `~/.ssh/` directory.

## 2. Setting Up a DigitalOcean and creating Droplet

1. Log in or create an account on [DigtalOcean](https://cloud.digitalocean.com/login).

2. Go to the Dashboard, and click **Create the Droplet**.

3. Choose **Ubuntu 16.04 x64** image from the list of given images.

4. Choose a preferred size. In this project, I have chosen the **1GB/1 vCPU/25GB** configuration.

5. In the section **Add Your SSH Keys**, paste the content of your public key, `id_rsa.pub`:

   This step will automatically create the file `~/.ssh/authorized_keys` with appropriate permissions and add your public key to it. It would also add the following rule in the `/etc/ssh/sshd_config` file automatically:

   ```
   PasswordAuthentication no
   ```

   This rule essentially disables password authentication on the `root` user, and rather enforces SSH logins only.

 6. Click **Create** to create the droplet. This will take some time to complete. After the droplet has been created successfully, a public IP address will be assigned. In this project, the public IPv4 address that I have been assigned is `159.65.151.202`.

## 3. Logging In as `root` via SSH and Updating the System

### 3.1. Logging in as `root` via SSH

After creating the droplet successfully, you can now log into the server as `root` user by running the following command in your host machine:

```
  $ ssh root@167.99.170.26
```

This will look for the private key in your local machine and log you in automatically if the corresponding public key is found on your server. After you are logged in, you might see something similar to this:

```
$ ssh root@167.99.170.26
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.4.0-131-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Sep 8 21:22:21 UTC 2018

  System load:  0.0               Processes:           98
  Usage of /:   7.5% of 24.06GB   Users logged in:     0
  Memory usage: 24%               IP address for eth0: 167.99.170.26
  Swap usage:   0%

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


Last login: Sun Sep 8 21:15:18 2018 from 106.78.64.224
root@ubuntu-18:~$

```
### 3.2. Updating the System

Run the following command to update the virtual server:

```
 # apt update && apt upgrade
```

This will update all the packages. If the available update is a kernel update, you might need to reboot the server by running the following command:

```
# reboot
```

## 4. Changing the SSH Port from 22 to 2200

1. Open the `/etc/ssh/sshd_config` file with `nano` or any other text editor of your choice:

   ```
   # nano /etc/ssh/sshd_config
   ```

2. Find the line `#Port 22` (would be located around line 13) and change it to `Port 2200`, and save the file.

3. Restart the SSH server to reflect those changes:
   ```
   # service ssh restart
   ```

4. To confirm whether the changes have come into effect or not, run:
   ```
   # exit
   ```

   This will take you back to your host machine. After you are back to your local machine, run:

   ```
   $ ssh root@167.99.170.26 -p 2200
   ```

   You should now be able to log in to the server as `root` on port 2200. The `-p` option explicitly tells at what port the SSH server operates on. It now no more operates on port number 22.

## 5. Configure Timezone to Use UTC

To configure the timezone to use UTC, run the following command:

```
# dpkg-reconfigure tzdata
```

It then shows you a list. Choose ``None of the Above`` and press enter. In the next step, choose ``UTC`` and press enter.

You should now see an output like this:

```
Current default time zone: 'Etc/UTC'
Local time is now:      Thu Sep 8 11:04:59 UTC 2018.
Universal Time is now:  Thu Sep 8 11:04:59 UTC 2018.
```

## 6. Creating the User `grader` and Adding it to the `sudo` Group

### 6.1. Creating the User `grader`

While being logged into the virtual server, run the following command and proceed:

```
  # adduser grader
```

The output would look like this:

```
  Adding user `grader' ...
  Adding new group `grader' (1000) ...
  Adding new user `grader' (1000) with group `grader' ...
  Creating home directory `/home/grader' ...
  Copying files from `/etc/skel' ...
  Enter new UNIX password:
  Retype new UNIX password:
  passwd: password updated successfully
  Changing the user information for grader
  Enter the new value, or press ENTER for the default
	  Full Name []: Grader
	  Room Number []:
	  Work Phone []:
	  Home Phone []:
	  Other []:
  Is the information correct? [Y/n]
```

**Note**: Above, the UNIX password I have entered for the user `grader` is, `root`.

## 7. Adding `grader` to the Group `sudo`

Run the following command to add the user `grader` to the `sudo` group to grant it administrative access:

```
  # usermod -aG sudo grader
```
Run the following command also to eliminate errors.

```
$ sudo nano /etc/sudoers.d/grader
```
then add ```grader ALL=(ALL:ALL) ALL``` to the file then save and quit.

### 7.1. Adding SSH Access to the user `grader`

To allow SSH access to the user `grader`, first log into the account of the user `grader` from your virtual server:

```
# su - grader
```

You should see a prompt like this:

```console
grader@ubuntu-16:~$
```

Now enter the following commands to allow SSH access to the user `grader`:

```
$ mkdir .ssh
$ chmod 700 .ssh
$ touch .ssh/authorized_keys
$ nano .ssh/authorized_keys
```

After you have run all the above commands, go back to your local machine and copy the content of the public key file `~/.ssh/id_rsa.pub`. Paste the public key to the server's `authorized_keys` file using `nano` or any other text editor, and save.

```
$ chmod 644 authorized_keys
```
Run the Above command to restrict the access for other users.

After that, run `exit`. You would now be back to your local machine. To confirm that it worked, run the following command in your local machine:

```console
$ ssh grader@167.99.170.26 -p 2200
```

You should now be able to log in as `grader` and would get a prompt to enter commands.

Next, run `exit` to go back to the host machine and proceed to the following step to disable `root` login.


## 8. Disabling Root Login

1. Run the following command on your local machine to log in as `root` in the server:
   ```
   $ ssh root@167.99.170.26 -p 2200
   ```

2. After you are logged in, open the file `/etc/ssh/sshd_config` with `nano`:
   ```
   # nano /etc/ssh/sshd_config
   ```

3. Find the line `PermitRootLogin yes` and change it to `PermitRootLogin no`.

4. Restart the SSH server:
   ```
   # service ssh restart
   ```

5. Terminate the connection:
   ```
   # exit
   ```

6. After you are back to the host machine, when you try to log in as `root`, you should get an error:

   ```console
   $ ssh root@167.99.170.26 -p 2200
   root@167.99.170.26: Permission denied (publickey).
   ```

## 9. Setting Up the Firewall

Now we would configure the firewall to allow only incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123) in grader:

```
$ ssh grader@167.99.170.26 -p 2200
# sudo ufw allow 2200/tcp
# sudo ufw allow 80/tcp
# sudo ufw allow 123/udp
```

To enable the above firewall rules, run:

```
# sudo ufw enable
```

To confirm whether the above rules have been successfully applied or not, run:

```
# sudo ufw status
```

You should see something like this:

```
Status: active

To                         Action      From
--                         ------      ----
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)
```

## 10. Setup to install apache2,mod_wsgi and git
To install the Apache Web Server, run the following command after logging in as the `grader` user via SSH:

```
$ sudo apt-get update
$ sudo apt-get install apache2
$ sudo apt-get install libapache2-mod-wsgi python-dev
$ sudo a2enmod wsgi
```

To confirm whether it successfully installed or not, enter the URL `http://167.99.170.26` in your Web browser:

## 11. Configure Apache to serve a Python mod_wsgi application
Clone the Udacity-item-catalog app from Github
```
$ cd /var/www
$ sudo mkdir catalog
$ sudo chown -R grader:grader catalog
$ cd catalog
$ git clone https://github.com/HarsimranSingh60/Item-Catalog.git catalog
```

## 12. Install pip , setup virtual environment and also install Flask and its dependencies:

After activating virtualenv each and ever command should be run under it.
Install pip , virtualenv (in /var/www/catalog)
```
$ sudo apt-get install python-pip
$ sudo pip install virtualenv
$ sudo virtualenv venv
$ source venv/bin/activate(Rest of the commands should be excecuted in the virtual environment)
$ sudo chmod -R 777 venv
```

Install Python's PostgreSQL adapter psycopg2:
```
$ sudo apt-get install python-psycopg2
```

Configure and Enable a New Virtual Host
```
$ sudo nano /etc/apache2/sites-available/catalog.conf
```
Add the following content:
```
<VirtualHost *:80>
   ServerName 167.99.170.26
   ServerAlias 167.99.170.26.xip.io
   ServerAdmin grader@167.99.170.26
   WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
   WSGIProcessGroup catalog
   WSGIScriptAlias / /var/www/catalog/catalog.wsgi
   <Directory /var/www/catalog/catalog/>
       Order allow,deny
       Allow from all
   </Directory>
   Alias /static /var/www/catalog/catalog/static
   <Directory /var/www/catalog/catalog/static/>
       Order allow,deny
       Allow from all
   </Directory>
   ErrorLog ${APACHE_LOG_DIR}/error.log
   LogLevel warn
   CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Enable the new virtual host:

```
$ sudo a2ensite catalog
```
Create and configure the .wsgi File
```
$ cd /var/www/catalog/
$ sudo nano catalog.wsgi
```

Add the following content:

```
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")

from catalog import app as application
application.secret_key = 'super_secret_key'
```

The original app that were developed on local machine needs some tweaks in order to be deployed on Digital Ocean. The major modifications include:
1. Rename project.py to __init__.py
2. Update the absolute path of client_secrets.json in __init__.py(important)

## 13. Installing and Configuring PostgreSQL

Install some necessary Python packages for working with PostgreSQL:
```
$ sudo apt-get install libpq-dev python-dev.
```

Install PostgreSQL:
```
$ sudo apt-get install postgresql postgresql-contrib
```

PostgreSQL automatically creates a new user 'postgres' during its installation. So we can connect to the database by using postgres username with:
```
$ sudo -u postgres psql
```

1. Create a new user called 'catalog' with his password:``` # CREATE USER catalog WITH PASSWORD 'catalog';```
2. Give catalog user the CREATEDB permission: ```# ALTER USER catalog CREATEDB;```
3. Create the 'catalog' database owned by catalog user: ```# CREATE DATABASE catalog WITH OWNER catalog;```
4. Connect to the database: ```# \c catalog```
5. Revoke all the rights:``` # REVOKE ALL ON SCHEMA public FROM public;```
6. Lock down the permissions to only let catalog role create tables:``` # GRANT ALL ON SCHEMA public TO catalog;```
7. Log out from PostgreSQL:``` # \q```. Then return to the grader user:``` $ exit.```
8. Edit the moredata.py and database.py file:
9. Change engine = create_engine('sqlite:///menu.db') to engine = create_engine('postgresql://catalog:catalog@localhost/catalog')
10. Restart Apache server:

   ```
   $ sudo service apache2 restart
   ```

   Now you should be able to run the application at <http://167.99.170.26.xip.io/>.

## 14. Install the Flask using command:
```
$ sudo pip install Flask
```

## 15. Install the sqlalchemy using command:
```
$ sudo pip install sqlalchemy
```
## 16. Install the Oauth2client using command:
```
$ sudo pip install oauth2client
```
## 17. Install Requests using command:
```
$ sudo pip install requests
```
## 18. Final updates on Google Login to make the app run on DigitalOcean:

To get the Google login working there are a few additional steps:

1. Go to Google Dev Console
2. Sign up or Login if prompted
3. Go to Credentials
4. Select your Web Application name
5. Enter 'Project-name'
6. Authorized JavaScript origins = 'http://167.99.170.26.xip.io'
7. Authorized redirect URIs = 'http://167.99.170.26.xip.io/login' && 'http://167.99.170.26.io/gconnect'
8. Save your changes

 Restart Apache to launch the app:
 ```
 $ sudo service apache2 restart
 ```

# Debugging

If you are getting an _Internal Server Error_ or any other error(s), make sure to check out Apache's error log for debugging:

```
$ sudo cat /var/log/apache2/error.log
```
Make sure  you restart the server when you make any changes to your application.


I Recommend First try creating a simple Flask App and see if the app is working to ensure that your configuration is right, then clone in your repository and start working on it. If there is an error after cloning then the problem will mostly be with your cloned app. Also watch the videos from youtube to create a simple Flask App

# References

[1] <https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps>

[2] <http://terokarvinen.com/2016/deploy-flask-python3-on-apache2-ubuntu>

[3] <https://www.digitalocean.com/community/questions/can-i-create-a-clone-from-a-dropplet>



