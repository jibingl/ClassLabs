# Lab - LAMP Stack
A LAMP (Linux, Apache, MySQL, Perl/PHP/Python) is one of the most common software stacks for the web's most popular applications. Its generic software stack model has largely interchangeable components.

## Part 1 - Linux Server
1. Create a virutal machine running Ubuntu server 24.04
2. Update your server
   ```
   sudo apt update
   sudo apt upgrade
   ```

## Part 2 - Apache Web Server Setup
### Install Apache
The Apache web server is among the most popular web servers in the world. It’s well documented, has an active community of users, and has been in wide use for much of the history of the web, which makes it a great choice for hosting a website.
1. Install Apache (version 2) software:
   ```shell
   sudo apt install apache2
   ```
2. Confirm the installation by checking Apache versions and service status:
   ```shell
   apache2ctl -v
   systemctl status apache2
   ```

### Test Apache default settings
1. Ensure Apache2 is running. Open a browser from your host computer on which the Ubuntu server is running, then type *http://your-server-ip* (Note: Replace *your-server-ip* with your Ubuntu server's IP).
2. You will get the Apache default web page.

### Create a web page under Apache defualt settings
1. Next, let's create a custom web page by yourself in the default web server content directory which is */var/www/html/* and name it as **index1.html**. Web page files use http protocol which is based on html language and requires filename with an extention of *.html*.
   ```shell
   sudo vim /var/www/html/index1.html
   ``` 
2. Add below content into the html file.
   ```text
   NSA Winter 2024 Class

   This is the landing page of NSA 2024 Winter Afternoon Class.
   ```
3. Access the web page by typing *http://your-server-ip* in a browser. Observe the difference from the previous Apache default web page. (Screenshot required)

### Create a web page with custom configurations
1. Creating a *Virtual Host* config-file for your website.
   When using the Apache web server, you can create virtual hosts to encapsulate configuration details and host more than one domain from a single server. In this guide, we’ll set up a domain called nsa-w24-p2.local.
   ```
   sudo vim /etc/apache2/sites-available/nsa-w24-p2.conf
   ```
2. Add below content into the file.
   ```
   <VirtualHost *:80>
       ServerName localhost
       ServerAdmin webadmin@localhost
       DocumentRoot /var/www/nsa-w24-p2
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```
   With the line `DocumentRoot /var/www/nsa-w24-p2` in this VirtualHost configuration, we’re telling Apache to serve your_domain using **/var/www/nsa-w24-p2** as the web root directory.
3. To make sure your configuration file doesn’t contain syntax errors, run the following command:
   ```
   sudo apache2ctl configtest
   ```
4. You need to disable the default website that comes installed with Apache. Because Apache’s default configuration would override your virtual host. To disable Apache’s default website, type:
   ```
   sudo a2disstie 000-defualt.conf
   ```
5. Now, use a2ensite to enable the new virtual host:
   ```
   sudo a2ensite nsa-w24-p2.conf
   ```
6. Finally, reload Apache so these changes take effect:
   ```
   sudo systemctl reload apache2
   ```
7. Currently, the folder **/var/www/nsa-w24-p2** is not exist. Next, you need to create the folder, and then create a new web page html file inside.
   ```
   sudo mkdir /var/www/nsa-w24-p2/
   sudo vim /var/www/nsa-w24-p2/index.html
   ```
8. Insert below content into the new webpage file **index.html**: (You don't need to learn html language for this lab, just copy and paste html codes by now.)
   ```
  <html>
    <head>
      <title>NSA Winter 2024 Class</title>
    </head>
    <body>
      <h1>NSA Winter 2024 Class</h1>
       
      <p>This is the landing page of <strong>NSA 2024 Winter Afternoon Class</strong>.</p>
    </body>
  </html>
  ```

9. Save and close the file. Open a broswer to access the new webpage at *http://your-server-ip* and observe the difference from the previous pages. (Screenshot required)

## Part 3 - MySQL Setup
Now you have a web server up and running. In most cases, you need to install the database system to be able to store and manage data for your site. MySQL is a popular database management system used within PHP environments.  
### Install MySQL Server
1. Install MySQL server
   ```
   sudo apt install mysql-server
   ```
2. Verify installation and check MySQL version.
   ```
   mysql -v
   ```
3. Secure MySQL server default settings by running below command. Securing the MySQL server is an important step when protecting databases from unauthorized user access. In the following steps, disable insecure defaults to secure your database server.
   ```
   sudo mysql_secure_installation
   ```
4. When prompted, input answers as below:
   Questions | Answers | Notes |
   ----------|---------|-------|
   VALIDATE PASSWORD component: | No | Not best practice but for lab convinience.
   Remove anonymous users?: | Yes | to revoke MySQL console access to unknown database users.
   Disallow root login remotely?: | Yes | to disable remote access to the MySQL root user account on your server.
   Remove test database and access to it?: | Yes | to delete the MySQL test databases.
   Reload privilege tables now?: | Yes | to refresh the MySQL privilege tables and apply your new configuration changes.





## Part 4 - PHP Setup
--------------------
sudo apt install apache2 mysql php php-mysql
(Optional) sudo apy install libapache2-mod-php

Verify services versions and status


2- Secure the MySQL Server
-------------------------


sudo mysql_secure_installation

:

sudo systemctl restart mysqld


3- Access MySQL
---------------
sudo mysql

mysql> show databases;
mysql> use mysql;
mysql> show tables;
mysql> select * from user;
mysql> select Host,User,account_locked from user;
mysql> show processlist;
mysql> use information_schema;
mysql> show processlist;





6- Test PHP
-----------
sudo vim /var/www/nsa-w24-p2/info.php
<?php
phoinfo();

http://<your-linux-server-ip>/php.info


7- Test PHP connectiong to MySQL
--------------------------------
If you want to test whether PHP is able to connect to MySQL and execute database queries, you can create a test table with test data and query for its contents from a PHP script. Before you do that, you need to create a test database and a new MySQL user properly configured to access it.

Create a database named example_database and a user named example_user. You can replace these names with different values.

First, connect to the MySQL console using the root account:
sudo mysql

To create a new database, run the following command from your MySQL console:
mysql> create datebase labs_db;

Now create a new user and grant them full privileges on the custom database you’ve just created.

The following command creates a new user named example_user that authenticates with the caching_sha2_password method. We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.
mysql> create user 'sql_admin'@'localhost' identified by 'Your-password';

Now give this user permission over the example_database database:
mysql> grant all on mysql.* to 'sql_admin'@'localhost';

This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.

Now exit the MySQL shell.

Test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:
mysql -u sql_admin -p

Notice the -p flag in this command, which will prompt you for the password used when creating the example_user user. After logging in to the MySQL console, confirm that you have access to the example_database database:
mysql> show databases;


Now you can create the PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor:
sudo vim /var/www/nsa-w24-p2/con-php-mysql.php

The following PHP script connects to the MySQL database and queries for the content of the labs_db table, exhibiting the results in a list. If there’s a problem with the database connection, it will throw an exception.

Add this content into your con-php-mysql.php script, remembering to replace the user and password with your own:
<?php
$servername = "localhost";
$username = "sql_admin";
$password = "Your-password";
$database = "mysql";
$table = "user";

try {
  $conn = new PDO("mysql:host=$servername;dbname=$database", $username, $password);
  echo "<h2>User Accounts of MYSQL</h2><ol>";
  foreach($conn->query("SELECT Host,User,account_locked FROM $table") as $row) {
    echo "<li>" . $row['Host'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}

http://<your-server-ip>/con-php-mysql.php

sudo ufw app list
sudo ufw status
