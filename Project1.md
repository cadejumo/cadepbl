
#WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

I signed into my AWS free tier account and launched a new EC2 instance using a t2.micro on the Ubuntu Server 20.04 LTS. Within the instance i set my SG to SSH and i also set up HTTP on port 80. I created a new KP and saved in.

I then proceeded to SSH into my instance using MobaXterm client.
*below is a screenshot of ssh being connected*

![ssh pic](https://user-images.githubusercontent.com/78991413/132376648-0efc5521-529f-43e3-8d6a-ebcc33477057.png)

Next step was to install package update by running sudo apt update followed up installing apache by running sudo apt install apache2 -y to bypass the confirmation of installing apache
To confirm Apache had been installed correctly i used the command sudo systemctl status apache2 *as confimred below*

![apache confirmation](https://user-images.githubusercontent.com/78991413/132378203-e845b7f8-4c8b-4451-a215-14f7f0efab88.png)

below is the security groups i had opened.port 22 being for SSH and port 80 being for HTTP so i can access pages on the internet

![SG screenshot](https://user-images.githubusercontent.com/78991413/132386787-8ad2a10c-d1d0-43e5-8e1b-82e8539bac8c.png)

Used the public IP address from my EC2 instance to confirm that my web server has been correctly installed

![apache default page](https://user-images.githubusercontent.com/78991413/132388085-637c99e2-c56d-406e-8bd3-db646bbac7c8.png)


INSTALLED MYSQL
I installed a Database Managment System so i can store & manage data for my site in a relational database. MySQL is a popular relational database management system used within PHP environments. I ran the command sudo apt install mysql-server
ran a security script that comes pre-installed with MySQL sudo mysql_secure_installation

To test that it worked fine, i ran the command sudo mysql *as seen below*

![instal my sql](https://user-images.githubusercontent.com/78991413/132389301-97cfc002-9a46-401a-a25a-48649ce3d8c4.png)

To exit the database i used mysql> exit

INSTALLING PHP
PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, my server will need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. My server will also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies
I installed the 3 packages by running the command sudo apt install php libapache2-mod-php php-mysql

to confirm i ran php -v

![PHP confirmed](https://user-images.githubusercontent.com/78991413/132390288-2981f510-9766-429c-b05d-f23736ed060d.png)

VIRTUAL HOST
i configured a virtual host for my website using apache with the domain name 'projectlamp'
Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory.
I created my own directory for project lamp using sudo mkdir /var/www/projectlamp
Assigned ownership of the directory with your current system user using sudo chown -R $USER:$USER /var/www/projectlamp
Created and opened a new configuration file in Apache's sites available directory using sudo vi /etc/apache2/sites-available/projectlamp.conf

This created a file in a text editor. press 'i' to insert text

![VI](https://user-images.githubusercontent.com/78991413/132391305-91def163-e4a9-4ca9-90a3-f2f980f6505f.png)

To save and close the file, simply follow the steps below:

Hit the esc button on the keyboard
Type :
Type wq. w for write and q for quit
Hit ENTER to save the file

I entered the following command to show the new file in the sites available directory sudo ls /etc/apache2/sites-available

![sites available](https://user-images.githubusercontent.com/78991413/132391874-54a758d3-15f3-43a3-a96a-2514a792456f.png)

Enabled new virtual host by using the command sudo a2ensite projectlamp

![site PL enabled](https://user-images.githubusercontent.com/78991413/132392412-dad29a96-6765-4a2d-b449-d00a18d44cc1.png)

I disabled the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host. To disable Apache’s default website use a2dissite command , type:

sudo a2dissite 000-default Screen shot below showing Apache default website disabled

![disaabling default](https://user-images.githubusercontent.com/78991413/132392830-4b987225-8cc0-41b6-baee-00d7e6b0a7f0.png)

To make sure your configuration file doesn’t contain syntax errors, run: sudo apache2ctl configtest

![0 syntax errors](https://user-images.githubusercontent.com/78991413/132393066-87b9f97e-1de2-4b71-9849-69f8b116e9a3.png)

For the changes to take effect, i ran the command sudo systemctl reload apache2
Created an index.html file in that location so that i can test that the virtual host works as expected, by typing this command ~$ cd /var/www/projectlamp :/var/www/projectlamp$ touch index.html Ran the command below to set my virtual host default web page when my public IP Address URL is launched on my computer web browser.

![brower projectlamp](https://user-images.githubusercontent.com/78991413/132394285-feb8b488-dce7-4d8b-8510-df0b72b77710.png)

ENABLE PHP ON THE WEBSITE

I need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

Entered the command below

sudo vim /etc/apache2/mods-enabled/dir.conf

<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

After saving the file i reloaded apache2 so the change can take effect using sudo systemctl reload apache2

Created a PHP test script file known as ‘index.php’ to confirm that Apache is able to handle and process requests for PHP files.

vim /var/www/projectlamp/index.php

Entered the text below 
<?php
phpinfo();

saved and closed the file and refreshed my EC2 public IP address

![PHP page](https://user-images.githubusercontent.com/78991413/132395977-f153831d-4747-433e-a08e-933c1f482190.png)
