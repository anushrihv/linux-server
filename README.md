# linux-server
Linux Server Configuration
Set up using Digital ocean
•	Ip address is :45.55.47.62
•	URL to the server : http://45.55.47.62/

Setting up Digital Ocean
Go to https://www.digitalocean.com/ sign up and create a droplet .

1. Login as root to server
In your vagrant environemnt log in to droplet using ssh user@ip -i dropletname

2. Add a new user
•	As root, type sudo adduser grader. Then follow prompts to add a password and name for this new account.
•	Confirm addition of the new user by typing sudo cat /etc/passwd, the user grader should be listed in the output.

3. Give grader sudo permission
•sudo s (alternate way)
•	Reference Documentation: https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps

Make sure you have another terminal logged in as root user

Open new terminal and login as grader using ssh grader@ip -i dropletname

4. Set-up Key-based Authentication/Public Key Encryption
•	Using ssh-keygen on your local machine, create a key for the user grader. 
•	Follow the prompts to provide a name for this key and use the default key location (~/.ssh). 
•	This process will create two keys on your local machine, the file with extension .pub is the public key to be transferred to the server.

5. Add Public Key to Server (Droplet)
•	Login to the server as the new grader user
•	Run these commands in your home directory
mkdir .ssh
touch .ssh/authorized_keys
•	Copy-and-paste the contents of the .pub key file created on your local machine above to the server as the contents of the authorized_keys file.

6. Set-up Permissions on the .ssh file while logged in as grader
Run these commands:
chmod 700 .ssh
chmod 664 .ssh/authorized_keys
•	Then from the local computer, login to server using an additional flag to indicate the key file: 
•	ssh student@127.0.0.1 -p 2222 â€“i ~/.ssh/linuxCourse

7. Disable Password Based Login and Force Login using Key Pair
•	On the server, logged in as the student user, edit the sshd_config file 
•	sudo nano /etc/ssh/sshd_config
•	Change the line with Password Authentication from yes to no
•	This is read only when the service starts, so to restart the ssh service 
•	sudo service ssh restart

Disable remote login of root user
•	On the server, logged in as root, edit the sshd_config file 
•	sudo nano /etc/ssh/sshd_config
•	Change to PermitRootLogin no
•	Enable grader user remote ssh login sudo nano /etc/ssh/sshd_config and add AllowUsers grader
•	Restart the ssh service sudo service ssh restart

8. Update all currently installed packages
•	List all the packages to update 
•	sudo apt-get update
•	Update the packages 
•	
•	
sudo apt-get upgrade
•	Further updates 
•	sudo apt-get dist-upgrade

9. Change the SSH port from 22 to 2200
•	Edit the file to change the SSH port from 22 to 2200. 
•	nano /etc/ssh/sshd_config
•	Restart the server 
•	service ssh restart ** Do not logout as root yet ** 
•	In a separate Terminal window, try to login as root using the new SSH port as: ssh -i ~/.ssh/udacity_key.rsa â€“p 2200 root@AWS_IP_ADDRESS 
•	Reference Documentation: https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-12-04 
•	https://wiki.knownhost.com/security/misc/how-can-i-change-my-ssh-port

10. Configure Firewall to only allow incoming connections for SSH (port 2200),
HTTP (port 80), and NTP (port 123)
•	Check current UFW status sudo ufw status This will show that UFW is Inactive.
•	Set-up UFW using these commands
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 2200/tcp (Note: Changed SSH above to port 2200)
sudo ufw allow www
sudo ufw allow 123/udp
sudo ufw enable
•	Check UFW status after updates sudo ufw status This will show that UFW is now active with the settings below:
To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
2200/tcp                   ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
123/udp                    ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
2200/tcp (v6)              ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)
123/udp (v6)               ALLOW       Anywhere (v6)
•	NOTE: During the grading process, requested to perform sudo ufw deny 22 to disable port 22_

11. Configure local Timezone to UTC
•	Start the configuration process 
•	dpkg-reconfigure tzdata
•	In the window that appears, use the arrow keys to Scroll to the bottom of the Continents list and select Etc or None of the Above and then in the second list, select UTC 
•	Confirm time change by typing date on the command line 
•	Reference Documentation: http://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt/138442 and https://help.ubuntu.com/community/UbuntuTime

12. Install and configure Apache to serve a Python mod_wsgi application
•	Install Apache sudo apt-get install apache2 

•	Confirm successful installation by visiting http://52.24.160.178/ (URL from Udacity Environment information for AWS instance). It should say "It Works" and display other Apache information on the page. 


•	Install Python mod_wsgi sudo apt-get install libapache2-mod-wsgi 


•	Install and Configure Demo WSGI app sudo nano /etc/apache2/sites-enabled/000-default.conf 

•	At the end of the <VirtualHost *:80> block, right before the closing add this line: 
WSGIScriptAlias / /var/www/html/myapp.wsgi 

•	Restart Apache sudo service apache2 restart
•	NOTE: After restart the Home page will return a 404, weâ€™ll fix that next by configuring Apache to serve WSGI application

13. Configure Apache to serve basic WSGI application to confirm installation of Apache and mod_wsgi
•	Create the file /var/www/html/myapp.wsgi as 
•	sudo nano /var/www/html/myapp.wsgi
•	Within this file, write the following application
def application(environ, start_response):     
	status = '200 OK'     
	output = 'Hello World!'      
	response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]     
	start_response(status, response_headers)      
	return [output] 
•	Refresh the page and the text in the script above will be displayed

14. Install and configure PostgreSQL
•	Install PostgreSQL 
•	sudo apt-get install postgresql postgresql-contrib
•	Check that remote connections are not allowed 
•	sudo less /etc/postgresql/9.3/main/pg_hba.conf 
•	By default, remote connections to the database are disabled for security reasons when installing PostgreSQL from the Ubuntu repositories.
•	Basic server set-up 
•	sudo -u postgres psql postgres
•	Set-up a password for user postgres 
•	\password postgres and enter a password
•	Reference documentation: https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps 
•	https://help.ubuntu.com/community/PostgreSQL 
•	https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04

15. Create a new database user named with limited permissions to the database
•	Connect to database as the user postgres sudo su - postgres
•	Type psql to generate PostgreSQL prompt
•	Create a new user 
•	CREATE USER catalog WITH PASSWORD 'your_passwd';
•	Confirm that the user was created 
•	\du
•	Reference Documentation: http://www.postgresql.org/docs/8.0/static/sql-createuser.html 
•	http://www.postgresql.org/docs/9.1/static/sql-createrole.html

16. Limit permissions to new database user
•	Run \du to see what permissions the user catalog has
•	To see possible user roles, type: \h CREATE ROLE
•	Update permissions for catalog user: 
ALTER ROLE catalog WITH LOGIN;
ALTER USER catalog CREATEDB;
•	Create the database 
•	CREATE DATABASE catalog WITH OWNER catalog;
•	Login to the database 
•	\c catalog
•	Revoke all rights REVOKE ALL ON SCHEMA public FROM public;
•	Grant only access to the catalog role 
•	GRANT ALL ON SCHEMA public TO catalog;
•	Exit out of PostgreSQL and the postgres user 
•	\q, then exit
•	Restart postgresql 
•	sudo service postgresql restart
•	Reference Documentation: https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps

17. Install Git
•	Install Git as 
•	sudo apt-get install git
•	Edit Git Configuration
git config --global user.name "Your Name"
git config --global user.email youremail@domain.com
•	Confirm by running git config --list
•	Reference Documentation: https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-14-04

18. Clone Udacity Project 3 App to this Digital ocean server
•	Create a folder inside the /var/www folder called "catalog" and cd into this folder, see commands below. Remember this is a Python Flask app and not just html.
cd /var/www 
sudo mkdir catalog 
cd catalog 
•	Clone repo for Udacity Project 3 (item-catalog): git clone https://github.com/twhetzel/item-catalog.git catalog The project is now at /var/www/catalog/catalog
•	Make sure the .git directory is not publicly accessible via a browser 
At the root of the web directory, add a .htaccess file and include this line: 
RedirectMatch 404 /\.git
•	Reference Documentation: http://stackoverflow.com/questions/6142437/make-git-directory-web-inaccessible

NOTE:Make sure you change the 'FlaskApp' to 'catalog' or which ever is the name of your app in the WSGI configuration file

19. Install Flask and create Virtual Environment for the Catalog App
sudo apt-get install python-pip
sudo pip install virtualenv
•	Give the following command (where venv is the name you would like to give your temporary environment): sudo virtualenv venv
•	Now, install Flask in that environment by activating the virtual environment with the following command 
source venv/bin/activate
•	Give this command to install Flask inside 
sudo pip install Flask
•	Run the following command to test if the installation is successful and the app is running 
sudo python __init__.py renamed project.py to init.py 
It should display â€œRunning on http://localhost:5000/â€� or "Running on http://127.0.0.1:5000/". 
If you see this message, you have successfully configured the app.
•	To deactivate the environment, give the following command 
deactivate
•	Configure and Enable the new Virtual Host sudo nano /etc/apache2/sites-available/DemoApp.conf
•	Add file contents for VirtualHost configuration, see Step 4 here: 
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
•	Enable the virtual host with the following command 

sudo a2ensite DemoApp
service apache2 reload
•	Create the .wsgi file
cd /var/www/DemoApp`
nano demoapp.wsgi
•	Add code per documentation
•	Restart Apache sudo service apache2 restart
•	Reference documentation: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

20. Add additional packages for the App
•	Go to directory for the catalog app
cd /var/www/catalog/catalog
sudo apt-get install python-pip
sudo pip install virtualenv
sudo virtualenv venv
•	Activate the environment 
source venv /bin/activate
•	Install App dependencies for Flask and Database
sudo pip install Flask
sudo pip install sqlalchemy
sudo pip install Flask-SQLAlchemy
sudo pip install psycopg2
sudo apt-get install python-psycopg2 
sudo pip install flask-seasurf
sudo pip install oauth2client
sudo pip install httplib2
sudo pip install requests

•	Configure Virtual Host in Apache sudo nano /etc/apache2/sites-available/catalog.conf
•	Enable this Virtual Host sudo a2ensite catalog Prompted to run: service apache2 reload to activate the new configuration
•	Configure the WSGI file
cd /var/www/catalog
sudo nano catalog.wsgi
•	Add this to the file:
#!/usr/bin/python 
import sys 
import logging 

logging.basicConfig(stream=sys.stderr) 
sys.path.insert(0,"/var/www/catalog/")  

from catalog import app as application 
application.secret_key = 'super_secret_key'
•	Restart Apache sudo service apache2 restart
•	Modify the database calls in the catalog app to use PostgreSQL vs. SQLite 
Edit these files: database_setup.py, project.py, and lotofevents-users.py 
Remove: engine = create_engine(â€˜sqlite:///androidevents.dbâ€™, echo=True) 
Add: engine = create_engine('postgresql://catalog:catalog_passwd@localhost/catalog')
•	Use the full path to client_secrets.json and fb_client_secrets.json in the project.py file
•	Reference Documentation: http://docs.sqlalchemy.org/en/rel_1_0/core/engines.html#postgresql

21. Update OAuth Information for Google+ and Facebook Logins
•	Google+
◦	Go to: https://console.developers.google.com/home/dashboard?project=udacity-1065
◦	Click on Enable and Manage APIs, then click on Credentials in the left-hand menu
◦	Select Catalog App
◦	Add URLs to 
Authorized Javascript origins, both local URL and EC2 version, e.g. http://52.24.160.178 and http://ec2-52-24-160-178.us-west-2.compute.amazonaws.com/
Authorized redirect URIs, http://ec2-52-24-160-178.us-west-2.compute.amazonaws.com/login and http://ec2-52-24-160-178.us-west-2.compute.amazonaws.com/gconnect
◦	NOTE: Needed to restart Apache and Python app to get it all working
◦	Downgrad packages to enable Google+ Login
•	pip install werkzeug==0.8.3 
•	pip install flask==0.9 
•	pip install Flask-Login==0.1.3
•	
◦	Reference Documentation: https://discussions.udacity.com/t/oauth-course-google-sign-in-doesnt-work/15444
•	Facebook
◦	Go to https://developers.facebook.com/apps
◦	Click on Android Events app
◦	Click on Settings and navigate to Valid OAuth redirect URIs section
◦	Add URLs for local and EC2 instance and then Save

Exceeds Specifications

Add support for unattended package updates 
sudo apt-get install unattended-upgrades
•	Edit /etc/apt/apt.conf.d/50unattended-upgrades and uncomment line for "${distro_id}:${distro_codename}-updates";
•	Edit /etc/apt/apt.conf.d/10periodic and add this line 
•	APT::Periodic::Unattended-Upgrade "1"; 
•	Also, change the AutocleanInterval from 0 to 7
•	The results of unattended-upgrades will be logged to /var/log/unattended-upgrades
•	Confirm by looking for file in this directory 
•	sudo ls /var/log/unattended-upgrades
•	Reference Documentation: https://help.ubuntu.com/12.04/serverguide/automatic-updates.html

Configure firewall to monitor for unsuccessful login attempts
•	Install Fail2Ban
sudo apt-get update
sudo apt-get install fail2ban
•	Copy jail.conf to local copy since .conf can be modified by package updates 
•	sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
•	Edit file 
•	sudo nano /etc/fail2ban/jail.local
•	Start the service 
•	sudo service fail2ban start
•	Test by trying to ssh as a fake user, e.g. ssh fakeUser@52.24.160.178 -p 2200 and then in a different terminal run sudo iptables â€“S and the banned user should be displayed in the list
•	Reference Documentation: https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-14-04
•	NOTE: If setting up email notice, install sudo apt-get install sendmail iptables-persistent

Application status monitoring
•	Install Glances sudo pip install glances
•	Run Glances by typing glances
Reference Documentation: https://pypi.python.org/pypi/Glances and http://glances.readthedocs.org/en/latest/glances-doc.html#web-server-mode
