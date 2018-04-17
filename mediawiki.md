1- install linux (ubuntu 17.10)
2- update and upgrade git
	sudo apt-get update
	sudo apt-get upgrade

3- install apache2
	sudo apt-get install apache2
   
   After installing Apache2, run the commands below to disable directory listing.
	sudo sed -i "s/Options Indexes FollowSymLinks/Options FollowSymLinks/" /etc/apache2/apache2.conf

   restart and enable apache2
	sudo systemctl stop apache2.service
	sudo systemctl start apache2.service
	sudo systemctl enable apache2.service

4- install mariadb
	sudo apt-get install mariadb-server mariadb-client

   then, stop, start and enable mariadb.
	sudo systemctl stop mariadb.service
	sudo systemctl start mariadb.service
	sudo systemctl enable mariadb.service

   secure mariadb server.
	sudo mysql_secure_installation

   then, answer the fellowing questions:-
	
    Enter current password for root (enter for none): Just press the Enter
    Set root password? [Y/n]: Y
    New password: Enter password
    Re-enter new password: Repeat password
    Remove anonymous users? [Y/n]: Y
    Disallow root login remotely? [Y/n]: Y
    Remove test database and access to it? [Y/n]:  Y
    Reload privilege tables now? [Y/n]:  Y

   Restart mariadb server:-
	sudo systemctl restart mariadb.service

5- install php and related moudles:- 
	sudo apt-get install php php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-mcrypt php-ldap php-zip php-curl

   now we install php7.2 to make sure the mediawiki server works with other servers thst support php7.2
	sudo apt install software-properties-common
	sudo add-apt-repository ppa:ondrej/php -y 
	sudo apt update
	sudo apt install php7.2 php7.2-fpm php7.2-mysql php-common php7.2-cli php7.2-common php7.2-json php7.2-opcache php7.2-readline php7.2-mbstring php7.2-xml php7.2-gd php7.2-curl

   then we start php7.2, enable auto start when system boots and check it's status.

	sudo systemctl start php7.2-fpm
	sudo systemctl enable php7.2-fpm
	systemctl status php7.2-fpm
   
   now we create test file to php, we name it test.php
	sudo nano /usr/share/nginx/html/test.php
   Paste the following PHP code into the file.
	<?php phpinfo(); ?>
   then we save and exit, now type in our url the ip address/test.phpto check if the php works.

6- now we create mediawiki database,
	sudo mysql -u root -p
   then we create database called wikinet;
	create database wikinet;
   now we create user(wikinetuser) for the database with the password.
	CREATE USER 'wikinetuser'@'localhost' IDENTIFIED BY 'new_password_here';
   now we grant the user full access to the database.
	GRANT ALL ON wikinet.* TO 'wikinetuser'@'localhost' IDENTIFIED BY 'user_password_here' WITH GRANT OPTION;
   lastly, save and exit.
	flush privileges;
	exit;

7- now we download mediawiki.
	cd /tmp && wget https://releases.wikimedia.org/mediawiki/1.29/mediawiki-1.29.0.tar.gz
   unzip mediawiki then download file to apache2 default root.
	sudo tar -zxvf mediawiki*.tar.gz
	sudo mkdir -p /var/www/html/mediawiki
	sudo mv mediawiki-1.29.0/* /var/www/html/mediawiki

   Change and modify the directory permission.
	sudo chown -R www-data:www-data /var/www/html/
	sudo chmod -R 755 /var/www/html/

8- config apache2
	sudo nano /etc/apache2/sites-available/mediawiki.conf
   then we type in the following contents.
	<VirtualHost *:80>
     ServerAdmin wikinet@example.com
     DocumentRoot /var/www/html/mediawiki/
     ServerName example.com
     ServerAlias www.example.com

     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined

	</VirtualHost>

9- now we enable mediawiki  site, and restart apache2.
	sudo a2ensite mediawiki.conf
	sudo systemctl restart apache2.service

10- finally, type in the url section ip address/mediawiki, that will take you to your server to finish up the setting it uo.
    click on set up the wiki
    then, type in the database name(wikinet), username(wikinetuser), and the password().
    finally, you going to download lacalsetting .php file and move it to the same location that you install your mediawiki to save the setting.

