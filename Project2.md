
PROJECT 2: LEMP STACK IMPLEMENTATION

INSTALLING THE NGINX WEB SERVER 

as like the previous project i launched a new EC2 instance on my Ubuntu 20.04 server opening up SSH and opening up HTTP through port 80. I proceeded to SSH into the server using the MobaXterm client 

![SSH pic1](https://user-images.githubusercontent.com/78991413/132716093-56d282a4-d18f-4c1b-8e78-247e609198d0.png)

i then proceeded to then update the servers package using the comman sudo apt update and then i installed the nginx web server in order to display web pages on the internet. I used the command sudo apt install nginx
To verify that nginx was successfully installed i ran the command sudo systemctl status nginx

![nginx installed](https://user-images.githubusercontent.com/78991413/132717956-f179479d-7fdb-47db-82bb-a5d1835eeb63.png)

The server is running but firstly i wanted to access it via my Ubuntu machine by doing curl http://IP:80

![curl ubuntu](https://user-images.githubusercontent.com/78991413/132718519-456ac543-f01b-498d-8154-15117e13d015.png)

due to it being available via my Ubuntu machine it should be also available via the web server being http://*aws public IP*:80

![nginx web server installed](https://user-images.githubusercontent.com/78991413/132718927-65c973fa-0f4c-4f73-910e-6872ca662251.png)

INSTALLING MYSQL

I now need to install a Database Management System to be able to store and manage data for my site in a relational database. MySQL is a popular relational database managment system used within PHP environments.

To install we use sudo apt install mysql-server -y 

when that is finished we run a security script that comes pre-installed. It will remove some insecure default settings and lock access to your database system. the command is sudo mysql_secure_installation

next i went to test whether i can log in by doing sudo mysql

![logged in mysql](https://user-images.githubusercontent.com/78991413/132742572-0af74bb9-03b6-4dc0-a957-e26c336bd329.png)

INSTALLING PHP

Nginx has been installed to serve my content and mysql to store and manage my data. I now installed php to process code and generate content for the web server. 
While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration.

I installed php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, I'll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

To install these 2 packages at once, run:

sudo apt install php-fpm php-mysql

CONFIGURING NGINX TO USE PHP PROCESSOR 

When using the Nginx web server we create server blocks to capture configuration details and host more than one domain on a single server. 

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if multiple sites are being hosted. Instead of modifying /var/www/html, i created a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.

Create the root web directory for your_domain as follows:

sudo mkdir /var/www/projectLEMP

Next, i assigned ownership of the directory with the $USER environment variable, which will reference your current system user:

sudo chown -R $USER:$USER /var/www/projectLEMP

Then i opeedn a new configuration file in Nginx’s sites-available directory using nano:

sudo nano /etc/nginx/sites-available/projectLEMP

#/etc/nginx/sites-available/projectLEMP

![nano text file](https://user-images.githubusercontent.com/78991413/132744738-60f08bf8-7899-4d18-b58b-bac023745be4.png)

I activated the configuration by linking to the config file from Nginx’s sites-enabled directory:

sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/

This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:

sudo nginx -t

![sudo nginx for syntax errors](https://user-images.githubusercontent.com/78991413/132745219-11a73a5e-e37e-480f-a895-43c187750497.png)

I also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

sudo unlink /etc/nginx/sites-enabled/default

I reloaded Nginx to apply the changes:

sudo systemctl reload nginx

The website is now active, but the web root /var/www/projectLEMP is still empty. I created an index.html file in that location so that we can test that your new server block works as expected:

sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html

i went back to refresh my public ip address on port80

![ip project lemp](https://user-images.githubusercontent.com/78991413/132745664-cac4a57e-7d2e-4bfd-995f-649505329cac.png)

TESTING PHP WITH NGINX
The LEMP stack is now be completely set up.
My LAMP stack is completely installed and fully operational.
I can test it to validate that Nginx can correctly hand .php files off to my PHP processor.
I did this by creating a test PHP file in my document root. Opened a new file called info.php within my document root in the text editor:

sudo nano /var/www/projectLEMP/info.php

This is valid PHP code that will return information about your server:

<?php
phpinfo();

![php info via ip](https://user-images.githubusercontent.com/78991413/132746650-3959515e-2113-4ede-936c-9306fc572fe1.png)

RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

i connected back into my DB using sudo mysql 

i created a new DB in mysql using mysql> CREATE DATABASE `example_database`;

i created a new user also by using the command mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';

I gave this user permission over the example_database database:

mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';

![created a user in database](https://user-images.githubusercontent.com/78991413/132748270-e4094821-9421-404d-b309-c769b207c7d7.png)

I tested if the new user had the proper permissions by logging in to mysql console again but instead using the custom user credentials 

mysql -u example_user -p

I used mysql> SHOW DATABASES; to show i have access to the DB

![i have access to the database](https://user-images.githubusercontent.com/78991413/132748849-fb615eb8-dfb8-413f-8679-6abc3212b0c7.png)

next i wanted to create a test table named todo_list

CREATE TABLE example_database.todo_list (
mysql>     item_id INT AUTO_INCREMENT,
mysql>     content VARCHAR(255),
mysql>     PRIMARY KEY(item_id)
mysql> );

to show it was successful i ran  

![input data on DB](https://user-images.githubusercontent.com/78991413/132749226-47d282b4-5dcb-4491-8165-f118e8c8917c.png)

i know created a php script that will connect to mysql and query for my content. nano /var/www/projectLEMP/todo_list.php

<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}

I can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

![web server to do list](https://user-images.githubusercontent.com/78991413/132749601-086019f0-09d8-4a09-8c22-8d5da03fca93.png)




