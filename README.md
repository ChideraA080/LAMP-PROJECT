
# MY WEBSTACK IMPLEMENTATION (LAMP STACK) IN AWS

The LAMP stack in web development is a set of software tools used to build and run web applications. It stands for:
1. Linux (Operating System)
2. Apache (Web Server)
3. MySQL (Database)
4. PHP (Programming Language)

In AWS, LAMP stack implementation means setting up these tools on an EC2 instance (a virtual server) to host and run websites or web applications.

This project demonstrates the setup of a LAMP (Linux, Apache, MySQL, PHP) stack in Amazon Web Services (AWS). I used an EC2 instance on AWS, connecting via PuTTY for SSH access to configure the stack.


##  Step 1: Launch an EC2 Instance

In my AWS console, I launched an EC2 instance using Ubuntu Server.
I configured the security group with:
1. SSH (Port 22) for remote access.
2. HTTP (Port 80) for web access.

![Lunch Instance](./Screenshot-of-Instance-running-with-Public-IP-Address.png)

## STEP 2: Connect to the EC2 Instance

I download PuTTY for Windows.
Use the public IP address of my EC2 instance and my private key to connect via SSH:

`ssh -i "Private-key.pem" ubuntu@my-ec2-public-ip`

![ssh connect with putty](./Screenshot-of-Establishing-SSH-Open-connection-using-PUTTY.png)
## STEP 3: Installing Apache and Updating the Firewall rules

I installed Apache on my EC2 instance and updated the firewall rules to allow HTTP and HTTPS traffic.

![Security group](./Screenshot-of-Security-Group-Firewalls.png)
![browser apache default page](./Screenshot-of-Apache-Installation.png)
## STEP 4: Installing MYSQL

Next, I installed MYSQL on my EC2 Instance by running
 
`sudo apt install mysql-server`

 `sudo mysql`

  After MySQL was installed, I secured it by running the following command:
 
  `bash sudo mysql_secure_installation`

This command helps to secure the database by removing insecure defaults and setting a strong password for the root user. I also set a Password for the root user for the database security.
## STEP 5: Installing PHP

At this point, Apache is already installed to serve content and MYSQL installed to store and manage data.PHP is the component of our setup that will process code to display dynamic content to the end user.
The following were installed:
1.php package
2.php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases.
3.libapache2-mod-php, to enable Apache to handle PHP files.By running these 3 packages at once:

`sudo apt install php libapache2-mod-php php-mysql`

After installation to confirm my PHP version I ran this command

`php -v`

At this ponit, the LAMP stack is completely installed and fully operational.The lamp stack checklist is now complete with:

 1. Linux (Ubuntu 24) - Installed on AWS EC2 instance
 2. Apache - Installed on EC2 instance
 3. MySQL - Installed on EC2 instance
 4. PHP - Installed on EC2 instance

 
But to complete the LAMP stack implementation, I needed to configure an Apache Virtual Host to serve multiple websites on the same server.
##  STEP 6:  Creating a virtual host for the website using Apache

The default directory serving the apache default page is /var/www/html. I will create a new virtual host to serve a different website.

1. Adding a new Apache Virtual host

I created new directory for projectlamp using "mkdir" command

`sudo mkdir /var/www/projectlamp`

 Assigning ownership of directory to the Ubuntu server
I assigned the directory ownership with $USER environment variable which references the current system user.

`sudo chown -R $USER:$USER /var/www/projectlamp`

 Create a New Virtual Host File

`sudo vim /etc/apache2/sites-available/projectlamp.conf`

I added the following bare-bones configuration to the block file

```
<VirtualHost *:80>

  ServerName projectlamp
 
  ServerAlias www.projectlamp
 
  ServerAdmin webmaster@localhost
 
  DocumentRoot /var/www/projectlamp
 
  ErrorLog ${APACHE_LOG_DIR}/error.log
 
  CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

The configuration defines how Apache will handle requests for the projectlamp website, specifying the domain names, file locations, and where to log errors and access details. This setup ensures that the server correctly routes traffic and keeps track of any issues or visitor information for the website hosted at /var/www/projectlamp.

 Enable the New Virtual Host

`sudo a2ensite projectlamp`

 Disable apache’s default website.

This is needed to prevent conflicts between the default 000-default.conf and the new projectlamp.conf virtual host files. Also Apache’s default configuration will overwrite the virtual host if not disabled.

`sudo a2dissite 000-default`

To make sure my configuration file that doesn't contain syntax error I use this command

`sudo apache2ctl configtest`

Finally, I reload the apache2 server to apply the changes by running this command below

 `sudo systemctl reload apache2`

 The new website is now active but the web root /var/www/projectlamp is still empty. Create an index.html file in this location so to test the virtual host work as expected.

I create an index.html file in the /var/www/projectlamp directory using this command below

```
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
```

Open the website on a browser using the public IP address.

http://54.162.59.232

![browse http port80](./Screenshot-of-Virtual-host-created-using-Apache-to-display-on-website.png)
##  step 7: Enable PHP on the website

By default, Apache will prioritize index.html over index.php when serving web pages. This allows you to set up a temporary maintenance page with an index.html file, which visitors will see instead of the usual application page. After maintenance, you can remove or rename index.html to restore access to the application. To change this behavior, you need to edit the /etc/apache2/mods-enabled/dir.conf file and adjust the order of files listed in the DirectoryIndex directive.

1. Open the dir.conf file with vim to change the behaviour

`sudo vim /etc/apache2/mods-enabled/dir.conf`

I added below file:

  ```
  <IfModule mod_dir.c>
    # Change this:
    # DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
    # To this:
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
  </IfModule>
  ```

2. Reload Apache

Apache is reloaded so the changes takes effect.

`sudo systemctl reload apache2`

3 Create a php test script to confirm that Apache is able to handle and process requests for PHP files.

A new index.php file was created inside the custom web root folder with this command below.

`vim /var/www/projectlamp/index.php`

4. Add the text below in the index.php file using below command

```
<?php
phpinfo();
```

5.  Refresh the Page

![browser php versin with php page](./Screenshot-showing-Apache-can-handle-PHP-files-whe-PHP-script-was-successfully-added-and-installed.png)

 
## Challenges Faced


During the process, I encountered some issues with configuring security group rules, but by adjusting the inbound rules to allow necessary ports, I was able to resolve these.

## Conclusion

From the "Web Stack Implementation (LAMP STACK) in AWS" project, I have demonstrated my ability to successfully deploy and configure a web server using Linux, Apache, MySQL, and PHP on AWS. This project shows I can effectively set up a fully functional web environment, manage databases, handle server-side scripting, and perform essential security and maintenance tasks. It also highlights MY skill in managing remote servers using tools like PuTTY. Overall, it has proven my capability to create and manage a robust web application setup in the cloud.