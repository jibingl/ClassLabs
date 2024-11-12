# Lab - LAMP Stack
Explanation from the *wikipedia*:
> A LAMP (Linux, Apache, MySQL, Perl/PHP/Python) is one of the most common software stacks for the web's most popular applications. Its generic software stack model has largely interchangeable components.  
> Each letter in the acronym stands for one of its four open-source building blocks:
> - Linux for the operating system
> - Apache HTTP Server
> - MySQL for the relational database management system
> - Perl, PHP, or Python for the programming language
>
> The components of the LAMP stack are present in the software repositories of most Linux distributions.

## Part 1 - Linux Server
1. Create a virutal machine running Ubuntu server 24.04. If you already had one, skip this step.
   Specifics | Settings |
   ----------|----------|
   Virtual Memory | 2 GB |
   Virtual Processors | 1 processor * 2 cores |
   Virtual Network Adapter | NAT |
2. Update your server before continue.
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
3. Access the web page by typing *http://your-web-server-ip/index1.html* in a browser. Observe the difference from the previous Apache default web page. (Screenshot required)

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
9. Save and close the file. Open a broswer to access the new webpage at *http://your-web-server-ip* and observe the difference from the previous pages. (Screenshot required)


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
   VALIDATE PASSWORD component: | No | Not the best practice but for lab convinience.
   Remove anonymous users?: | Yes | to revoke MySQL console access to unknown database users.
   Disallow root login remotely?: | Yes | to disable remote access to the MySQL root user account on your server.
   Remove test database and access to it?: | Yes | to delete the MySQL test databases.
   Reload privilege tables now?: | Yes | to refresh the MySQL privilege tables and apply your new configuration changes.

### Access MySQL console
1. Sign into the MySQL server database by issuing below command. Without any options passed on **mysql** command, system uses local **root** account to access the MySQL console. 
   ```
   sudo mysql
   ```
   > Tips: A variant of above command is `sudo mysql -u root`.
2. Now you logged into MySQL server and got a prompt sign `mysql>`. By typing sql commands/languages, you can manage the database.
3. Next, try below sql commands to observe outputs.
   - Display all existing databases:
     ```
     mysql> show databases;
     ```
   - Select the database with name **mysql** so that you can do further operations on it: (Note: Here **mysql** is just a specific database name. It dosen't mean the MySQL server.)
     ```
     mysql> use mysql;
     ```
   - Display all existing tables of current selected database:
     ```
     mysql> show tables;
     ```
   - From current selected database, display all data stored in a table named **user**:
     ```
     mysql> select * from user;
     ```
   - The output of above sql directive are messed up with format. Because there are too much data columns stored in the table to properly display on a computer monitor. Use below command to choose certain columns to display:
     ```
     mysql> select Host,User,account_locked from user;
     ```
     > You can define any column in a sql command, but make sure the certain column exists and its name is correct. (e.g., `Host`, `User`, and `account_locked` are columns you can found in the table **user**.)
   - Display all connections to the database server:
     ```
     mysql> show processlist;
     ```
     #### Question 1: Can you find the connection currently you are using to interact with this database server? (Screenshot to show your answer)
4. Now exit the MySQL console.
   ```
   mysql> exit;
   ```


## Part 4 - PHP Setup
### Install PHP package
You have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the final user. In addition to the php package, you’ll need **php-mysql**, a PHP module that allows PHP to communicate with MySQL-based databases.
1. Run below command to install those packages
   ```
   sudo apt install php php-mysql
   ```
2. Verify services versions and status
   ```
   php -v
   ```
### Test PHP function
1. To test if PHP functions, create a **info.php** file in the directory */var/www/nsa-w24-p2/*:
   ```
   sudo vim /var/www/nsa-w24-p2/info.php
   ```
2. Insert below content:
   ```
   <?php
   phoinfo();
   ```
3. From your physical host machine, open a browser and access *http://your-web-server-ip/php.info*, if you get a page with tables showing PHP configurations, your PHP successfully installed.


### Test PHP connection to MySQL
To test whether PHP is able to connect to MySQL and execute database queries, we will create a new MySQL user properly configured to access some current data in MySQL.
1. First, connect to the MySQL console using the root account:
   ```
   sudo mysql
   ```
2. Now create a new user and grant them full privileges on the **mysql** database. The following command creates a new user named **sql_admin** that authenticates with the caching_sha2_password method. We’re defining this user’s password as **Your-password**, but you should replace this value with a secure password of your own choosing.
   ```
   mysql> create user 'sql_admin'@'localhost' identified by 'Your-password';
   ```
3. Now give this user permission over the **mysql** database. Below command will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.
   ```
   mysql> grant all on mysql.* to 'sql_admin'@'localhost';
   ```
4. Now exit the MySQL shell.
5. Test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:
   ```
   mysql -u sql_admin -p
   ```
6. Notice the `-p` flag in this command, which will prompt you for the password used when creating the **sql_admin** user.
7. After logging in to the MySQL console, confirm that you have access to the example_database database:
   ```
   mysql> show databases;
   ```

Now we are going to create a PHP script that will connect to MySQL and query for data.

8. Create a new PHP file (**con-php-mysql.php**) in your custom web root directory using your preferred editor:
   ```
   sudo vim /var/www/nsa-w24-p2/con-php-mysql.php
   ```
9. The following PHP script connects to the MySQL database and queries for the content of the **user** table, exhibiting the results in a list. If there’s a problem with the database connection, it will throw an exception.
10. Add this content into your **con-php-mysql.php** file, remembering to replace the **Your-password** with your own:
    ```
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
    ```
11. Save the php file. Access *http://your-we-server-ip/con-php-mysql.php* on your physical host machine. (Screenshot required)
12. From your Ubuntu server, sign in MySQL console as root and display all current connections to the database server.
    #### Question 2: Can you find the connection the webpage is using to query data from the **user** table? (Screenshot to show your answer)

##### Referrences:
1. [LAMP (software bundle)](https://en.wikipedia.org/wiki/LAMP_(software_bundle))
2. [How To Install Linux, Apache, MySQL, PHP (LAMP) Stack on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-lamp-stack-on-ubuntu)
3. [PHP Connect to MySQL](https://www.w3schools.com/php/php_mysql_connect.asp)
