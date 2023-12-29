# Nagios_installation

Installing Nagios Core and Nagios Plugin in Linux
If you follow these instructions correctly, you will end up with the following information.

Nagios and its plugins will be installed under the /usr/local/nagios directory.
Nagios will be configured to monitor a few services of your local machine (Disk Usage, CPU Load, Current Users, Total Processes, etc.)
Nagios web interface will be available at http://localhost/nagios



Step 1:#### Ensure that the prerequisite packages are installed on your system for the successful execution of Nagios. ####Install Apache and PHP Packages
We need to install Apache, PHP, and some libraries like gcc, glibc, glibc-common, and GD libraries and their development libraries before installing Nagios with the source. And to do so, we can use the yum package installer.




$sudo su
$yum install httpd php
$yum install gcc glibc glibc-common
$yum install gd gd-devel

Step 2: Create Nagios User and Group
Create a new nagios user using the useradd command and nagcmd group account and set a password.


$adduser -m nagios
$passwd nagios


Next, add both the nagios user and the apache user to the nagcmd group using the usermod command.
$groupadd nagcmd
$usermod -a -G nagcmd nagios
$usermod -a -G nagcmd apache



Step 3: Download Nagios Core and Nagios Plugin
Create a directory for your Nagios installation and all its future downloads.

$mkdir /home/nagios/
$cd /home/nagios/

Now download the latest Nagios Core and Nagios Plugins packages with the wget command.

$wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.4.9.tar.gz
$wget https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz

Check plugins or installed or not
$ls

Step 4: Extract Nagios Core and Nagios Plugins
We need to extract downloaded packages with the tar command as follows.

$tar -xf nagios-4.4.9.tar.gz
$tar -xf nagios-plugins-2.3.3.tar.gz

When you extract these tarballs, two new folders will appear in that directory.
$ls -la

Step 5: Installing and Configuring Nagios Core
Now, first, we will configure Nagios Core and to do so we need to go to the Nagios directory and run configure file if everything goes fine, it will show the output in the end as sample output.

$cd nagios-4.4.9/
$./configure --with-command-group=nagcmd

###ERROR###
for example: checking for SSL headers... configure: error: Cannot find ssl headers
[root@docker_server nagios-4.4.9]# yum install openssl-devel
#################Error resolved ######

After configuring, we need to compile and install all the binaries with the make all and make install commands, it will install all the needed libraries in your machine and we can proceed further.

$ make all
$ make install


The following command will install the init scripts for Nagios.
$ make install-init

To make Nagios work from the command line we need to install command-mode.
$ make install-commandmode

Next, install sample Nagios files, please run the following command.

$ make install-config

Step 6: Customizing Nagios Configuration
Open the “contacts.cfg” file with your choice of editor and set the email address associated with the nagiosadmin contact definition to receiving email alerts.

$ vi /usr/local/nagios/etc/objects/contacts.cfg


Step 7: Install and Configure the Web Interface for Nagios

We are done with all configurations in the backend, now we will configure the Web Interface For Nagios with the following command. The below command will Configure the Web interface for Nagios and a web admin user will be created “nagiosadmin”.

$ make install-webconf


In this step, we will be creating a password for “nagiosadmin”. After executing this command, please provide a password twice and keep it remember because this password will be used when you login into the Nagios Web interface.

$ htpasswd -s -c /usr/local/nagios/etc/htpasswd.users nagiosadmin


Restart Apache to make the new settings take effect.

$ systemctl restart httpd


Step 8: Compile and Install Nagios Plugin
go there and configure and install it as directed below.

$ cd /root/nagios
$ cd nagios-plugins-2.3.3
$ ./configure --with-nagios-user=nagios --with-nagios-group=nagios
$ make
$ make install

Step 9: Verify Nagios Configuration Files
Now we are all done with the Nagios configuration and it’s time to verify it to do so please insert the following command. If everything goes smoothly it will show up similar to the below output.

$ /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg


Step 10: Add Nagios Services to System Startup
To make Nagios work across reboots, we need to enable nagios and httpd at the system startup using the systemctl command.

$ systemctl enable nagios
$ systemctl enable httpd
Next, restart Nagios to make the new settings take effect.

$ systemctl restart nagios

#### Stop firewall or add ports  in firewall to access the nagios web interface #########

$ firewall-cmd --permanent --zone=public --add-service=http
$ firewall-cmd --permanent --zone=public --add-service=https
$ firewall-cmd --reload


Step 12: Log in to the Nagios Web Interface
Your Nagios is ready to work, please open it in your browser with “http://Your-server-IP-address/nagios” or “http://FQDN/nagios” and Provide the username “nagiosadmin” and password.

Nagios Web Dashboard
