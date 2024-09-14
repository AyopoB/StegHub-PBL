# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS
I worked on a LAMP(Linux, Apache, Mysql and Php) stack implementation on AWS EC2 t3.micro instance with **Ubuntu 24.04 LTS**. Below I  will outline the steps taken to carry out the Implementation.
- Step 0 - Preparing the prerequisites
- Step 1 - Installing Apache 
- Step 2 - Installing MySQL
- Step 3 - Installing PHP
- Step 4 - Creating a virtual Host for Your website using Apache
- Step 5 - Enabling PHP on the website
## STEP 0
**Prepare the prerequisites**

First create an AWS  account or login  to your existing AWS account and  create a virtual server with EC2 using Ubuntu Image.
Download the Server Key pair to your device 
launch your instance

Open your Terminal ( for this I made use of Git Bash for Windows) navigate to the location of the Key pair in the terminal using:

```cd Downloads```

Or whichever folder the Key pair was downloaded to.
Connect to the instance  through your Terminal  by running

```ssh -i "Private-key-name.pem" ubuntu@<Public IP address>```

Running just the above may not give desired results, so we need to to our AWS instance on the Console
locate Security group, Edit the inbound rules as shown below

![inbound rules](Images/edit%20the%20inbound%20rules.png)

After doing so, Run 


```chmod 400 "Private-key-name.pem"```

> The command `chmod 400 "Sample-server.pem"` is used to change the permissions of the file named `Sample-server.pem`.
> 
> Here's a breakdown of what it does:
> 
> - `chmod` is the command used to change file modes or permissions.
> - `400` is the permission setting you are applying. In this case, it means:
>   - **4** for read permission for the file owner (the user).
>   - **0** for no permissions for the group.
>   - **0** for no permissions for others.
> 
> So, `chmod 400` sets the file permissions so that only the file's owner has read access, and no one else (including the group and others) has any access. This is commonly used for files containing sensitive information, such as private keys or configuration files, to ensure they are not accessible by anyone other than the owner.

then connect to the instance  with

```ssh -i "Private-key-name.pem" ubuntu@<Public IP address>```

Now connecting to the EC2 instance with terminal is successful

![inbound rules](images/ubuntu%20terminal.png)

## STEP 1 
**Installing Apache**
Apache is a webserver which will be used to host our application
To install Apache, we use the Terminal(GitBash in this case)
. Run 

```sudo apt update```

 Then, install Apache with:

 ```sudo apt install apache2```

 To verify that Apache is running as a server  in our OS, we use the following command:

 ```sudo systemctl status apache2```

 ![alt text](images/Apache-running.png)

 Open your EC2 instance on the Console, copy the public IP address and paste to your browser.
 ![alt text](images/public%20url%20apache.png)
 This shows the  server is up and accessible online
 
## STEP 2
**Installing MySQL**

Install mySQL server with the following command
```sudo apt install mysql-server```

When the installation is finished, logged to  mySQL console with 

```sudo mysql```

Had to use a Alter user command to set password for root user

```ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';```

![alt text](images/mysql-secure.png)
After which I  exited the mysql console, now back to my Ubuntu server. I runned:

```sudo mysql_secure_installation```

![alt text](images/mysqlsecureinstall.png) 
The above  prompts with configurations for securing the MySQL server.
After  which I typed 

```sudo mysql -p```

![alt text](images/mysqlpwd.png)
It requested for the newly created password


## STEP 3
**Installing PHP**
I have Apache installed to serve my content and MySQL installed to store and manage my data. PHP is the component of my setup that will process code to display dynamic content to the final user.


To use PHP, I need to install the following:
- PHP package
- php-mysql: which allows PHP to communicate  with mysql-based databases
- libapache2-mod-php: to enable Apache to handle PHP files

The command below was used to install the above packages

```sudo apt install php libapache2-mod-php php-mysql```

![alt text](images/PHP%20install.png)
when installation is finished
Check the version with :

```php -v```
![alt text](images/phpver.png)

At this point, The LAMP Stack is fully installed.

## STEP 4
**Creating a virtual host for your website using Apache**

Firstly to create your virtual host, you need to name your project, for this  one  I used "mylampproject".

I created a directory for mylampproject using the command below

```sudo mkdir /var/www/mylampproject```

I then used the 'chown' command to assign ownership to the current system user using:

```sudo chown -R $USER:$USER /var/www/mylampproject```

Then proceeded to create and open a new configuration file in Apache sites-available directory  using vi (text editor) on the ubuntu server

```sudo vi /etc/apache2/sites-available/mylampproject.conf```

which created a blank file

```
<VirtualHost *:80>
    ServerName mylampproject
    ServerAlias www.mylampproject
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/mylampproject
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
The above was typed into the blank file, after which it was saved and the vi editor was exited.

I then used the a2ensite command to enable the new virtual host
![alt text](images/enablevhost.png)

After which Apache's default website was disabled
![alt text](images/disableapachedef.png)

Confirmed the config didn't contain any syntax error
![alt text](images/config%20tst.png)

Finally, Apache was reloaded so the changes take effect
![alt text](images/reloadapache.png)

The website was empty, so i created an index.html file using echo and the command below:

```  sudo echo 'Hello LAMP from hostname' $(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metada-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-hostname) 'With public IP' $(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/mylampproject/index.html```

 Open your EC2 instance on the Console, copy the public IP address and paste to your browser.
 ![alt text](images/test%20host.png)

## STEP 5
**Enabling PHP on the website**

With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. We need to change this behaviour  by doing the following:

```sudo vim /etc/apache2/mods-enabled/dir.conf```

we use the above command to edit and change the /etc/apache2/mods-enabled/dir.conf file and modify the order in which the index.php file is listed within the DirectoryIndex directive


Changed the existing one to the below.
```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
![alt text](images/changephp0.png)
After the file  was edited, the apache server was reloaded to reflect the changes.
```
sudo systemctl reload apache2
```

I created a new file called index.php in the custom web root folder
```
vim /var/www/mylampproject/index.php
```
after which i added to the  blank file created:
```
<?php
phpinfo();
```
I saved the file, went back to the public URL, refreshed the Web page
![alt text](images/php%20ino.png)
the above page was the result
This confirms that the PHP installation works fine

For security reasons the index.php page with php info has to be removed, We do that using the command below
```
sudo rm /var/www/your_domain/info.php
```
We refresh the webpage and it is back to:
![alt text](images/test%20host.png).



Following the steps in this documentation will enable anyone using it to set up a LAMP stack, configure and maintain the LAMP environment properly.
