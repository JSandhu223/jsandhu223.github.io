---
title: osTicket Part 1 - Installation on Debian 12
date: 2024-09-24 21:29:00 -0600
categories: [Ticketing Systems, Linux]
tags: [vm, tickets, linux, debian, sql, apache]     # TAG names should always be lowercase
description: This lab covers the prerequisites and installation of the open-source ticketing system osTicket in a virtual Linux environment.
---

<!--
<h2>Video Demonstration</h2>

- ### [YouTube: How To Install osTicket with Prerequisites](https://www.youtube.com)
-->

<h2>Environments and Technologies Used</h2>

- Oracle VM VirtualBox
- Debian
- Terminal
- Apache
- MariaDB

<h2>Operating System Used </h2>

- Debian 12.7.0

<h2>List of Prerequisites</h2>

- Debian `.iso` file
- CPU that supports Intel Virtualization Technology, AMD-V, or ARM Virtualization Extensions
- At least 2 GB of available memory
- 20 GB storage space

<h2>Installation Steps</h2>

<h3>Part 1 - Installing Debian</h3>

We'll first need to download and install VirtualBox from [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads). Once it is installed, we can head to [https://www.debian.org/](https://www.debian.org/) to download the latest version. Once the `.iso` file has finished downloading, we can go back to VirtualBox to configure a virtual machine running Debian. Click `New` to create a enter the setup wizard.

<img src="/assets/img/osticketprereqs/VirtualBox_CreateVM_1.png" alt="image" height="75%" width="75%" />

Give the VM a name of your choosing and make sure to select the Debian `.iso` file that we downloaded. For this tutorial, we will skip the unattended installation.

<img src="/assets/img/osticketprereqs/VirtualBox_CreateVM_2.png" alt="image" />

To find a balance between performance and hardware strain, we will allocate 2 GB of RAM and 2 CPU cores to the VM.

<img src="/assets/img/osticketprereqs/VirtualBox_CreateVM_3.png" alt="image" />

As Debian needs at least 10 GB of storage space to install the OS, we will allocate 20 GB of storage space for the VM.

<img src="/assets/img/osticketprereqs/VirtualBox_CreateVM_4.png" alt="image" />

Our virtual machine configuration should look something like this:

<img src="/assets/img/osticketprereqs/VirtualBox_CreateVM_5.png" alt="image" />

You should now see the VM in VirtualBox. Select the VM and click start to proceed to installing Debian.

<img src="/assets/img/osticketprereqs/VirtualBox_CreateVM_6.png" alt="image" height="75%" width="75%" />

For simplicity, we will choose to proceed with a graphical install.

<img src="/assets/img/osticketprereqs/DebianInstall_1.png" alt="image" />

Through the installation, feel free to use your language, location, and keyboard language. Now, when configuring the network, we'll stick with the default name.

<img src="/assets/img/osticketprereqs/DebianInstall_2.png" alt="image" height="75%" width="75%" />

Leave the domain field blank, as we will not be doing anything related to setting up a domain.

<img src="/assets/img/osticketprereqs/DebianInstall_3.png" alt="image" height="75%" width="75%" />

Proceed through the installer until you get to the partitioning disks step and select the first option.

<img src="/assets/img/osticketprereqs/DebianInstall_4.png" alt="image" height="75%" width="75%" />

Then select the virtual hard disk we created earlier to install Debian on.

<img src="/assets/img/osticketprereqs/DebianInstall_5.png" alt="image" height="75%" width="75%" />

When it asks how we want to partition our drive, stick the default partition.

<img src="/assets/img/osticketprereqs/DebianInstall_6.png" alt="image" height="75%" width="75%" />

Select `Yes` when it asks to format the disks.

<img src="/assets/img/osticketprereqs/DebianInstall_7.png" alt="image" height="75%" width="75%" />

When asked to install additional installation media, select `No`.

<img src="/assets/img/osticketprereqs/DebianInstall_8.png" alt="image" height="75%" width="75%" />

Proceed until the software installation screen, which we can leave as the default options. Note that we will be installing Apache web server later which is why we don't select to install the web server provided by Debian.

<img src="/assets/img/osticketprereqs/DebianInstall_9.png" alt="image" height="75%" width="75%" />

Select `Yes` when asked to install the GRUB boot loader.

<img src="/assets/img/osticketprereqs/DebianInstall_10.png" alt="image" height="75%" width="75%" />

Then select the partition that was created earlier in the installation.

<img src="/assets/img/osticketprereqs/DebianInstall_11.png" alt="image" height="75%" width="75%" />

Once this step has finished, you will be greeted with this screen indicating the installation has finished. Simply click on `Continue` to reboot the machine.

<img src="/assets/img/osticketprereqs/DebianInstall_12.png" alt="image" height="75%" width="75%" />

When the VM has restarted, log in to your account created during the installation wizard. Welcome to Debian!

<img src="/assets/img/osticketprereqs/Debian_Desktop.png" alt="image" height="75%" width="75%" />

<h3>Part 2 - Checking Updates</h3>

Before installing any applications, it is best practice to check for updates for packages. Note that if you created the root user during installation, our user account we logged in with won't have `sudo` permissions. To add ourselves to the list of `sudoers`, enter super user mode in the `Terminal` and enter the command `su`. Then navigate to `/etc`.

<img src="/assets/img/osticketprereqs/Add_Sudoer_1.png" alt="image" height="60%" width="60%" />

Then add your username to the file `sudoers`. Note that you may need to change file permissions to allow write access! The file should looks as follows:

<img src="/assets/img/osticketprereqs/Add_Sudoer_2.png" alt="image" height="60%" width="60%" />

We can now run with sudo permissions! Now, run `sudo apt update` to check for any upgrades. Then run `sudo apt upgrade` to upgrade the any outdated packages.

<h3>Part 3 - Installing Apache Web Server</h3>

To install Apache, run

`sudo apt install apache2`

Once installed, we can enable and start the apache web server

`sudo systemctl enable apache2`

`sudo systemctl start apache2`

To view the status of apache, we can run

`sudo systemctl status apache2`

<img src="/assets/img/osticketprereqs/Apache_StatusRunning.png" alt="image" height="60%" width="60%" />

For troubleshooting purposes, we can restart apache by running

`sudo systemctl restart apache2`

To shut off the apache web server, we can run

`sudo systemctl stop apache2`

With Apache installed, we can host osTicket and allow users to use the service through their web browser.

<h3>Part 4 - Installing MariaDB</h3>

We need a SQL database server to store information like user accounts, admin accounts, ticket information, etc. We can install MariaDB, as this is the most compatible version of the default MySQL database server that runs on Debian. To install MariaDB, run

`sudo apt install mariadb-server`

<h3>Part 5 - Installing PHP</h3>

PHP will be used to display content to the end-user and process requests to the database server. To install PHP and its necessary dependencies for osTicket, run

`sudo apt install apt install apache2 php php-cli php-common php-imap php-redis php-snmp php-xml php-zip php-mbstring php-curl php-mysqli php-gd php-intl php-apcu libapache2-mod-php unzip -y`

To confirm that PHP was installed, run `php -v` to see the version of PHP. As of this tutorial, the latest version is 8.2.20.

<img src="/assets/img/osticketprereqs/PHP_VersionCheck.png" alt="image" height="60%" width="60%" />

<h3>Part 6 - Creating the Database</h3>

We can create the database from the command line. Type `sudo mariadb` to open the mariaDB interpreter and enter the following SQL commands:

```
CREATE DATABASE osticket;
GRANT ALL PRIVILEGES ON osticket.* TO osticket@localhost IDENTIFIED BY "Password1";
FLUSH PRIVILEGES;
```

To show all database schemas that exist, type `SHOW DATABASES;`. We can see our `osticket` database is present along with some other default created databases.

<img src="/assets/img/osticketprereqs/MariaDB_Show_Databases.png" alt="image" height="60%" width="60%" />

We have now created the database that will store all information related to osTicket. To exit the MariaDB command line interpreter, type `EXIT;`. We will revisit MariaDB in a coming lab when we create some users.

<h3>Part 7 - Installing osTicket</h3>

First, we need to navigate to the root directory of the Apache web document located at `/var/www/html`. This directory is where webpage content lives so that Apache can fetch it and serve it to the client. It is here that we will install osTicket. Go to [https://osticket.com/download/](https://osticket.com/download/) and download the free version to get the zip file. When downloaded, move the zip file to `/var/www/html`. For convenience, I will do this in a separate tab.

<img src="/assets/img/osticketprereqs/move_zip_file.png" alt="image" height="60%" width="60%" />

As of this tutorial, the latest version of osTicket is **1.18.1**. Next, we go back to the `/var/www/html` directory and unzip the contents of the zip file by running

`sudo unzip osTicket-v1.18.1.zip -d osTicket`

This unzips the content into another folder called `osTicket`. Now that we have unzipped the zip file, we can delete it with the `rm` command. Next, navigate to to the directory `/var/www/html/osTicket/upload/include` and run

`sudo cp ost-sampleconfig.php ost-config.php `

This command copies the contents of the provided sample config file into a new file which we named `ost-config.php`.

To allow Apache the proper permissions to access web files, we need to change the ownership of all files in the `osTicket` folder. Apache uses the special user and group name of `www-data`. To change the ownership of all files and folders within the `osTicket` folder, run

`sudo chown -R www-data:www-data /var/www/html/osTicket/`

If you see the output `ls -l`, it should show the owners as `www-data`.

<img src="/assets/img/osticketprereqs/chown_result.png" alt="image" height="60%" width="60%" />

We also want to assign full rwx (read, write, execute) permissions to the 'www-data' user and group, while giving only rx (read and execute) permissions to everyone else. We can run

`sudo chmod -R 775 /var/www/html/osTicket/`

For a better understanding of Linux file permissions, see this guide: [https://www.stationx.net/linux-file-permissions-cheat-sheet/](https://www.stationx.net/linux-file-permissions-cheat-sheet/)

<img src="/assets/img/osticketprereqs/chmod_result.png" alt="image" height="60%" width="60%" />

<h3>Part 8 - Configuring Apache</h3>

We now need to connect Apache so that it can talk to osTicket. We can accomplish this with a virtual host config file. Navigate to `/etc/apache2/sites-available` and run `sudo touch osticket.conf`. This will create a file with with the name `osticket.conf`. Then using your favorite command line text editor, paste the following code snippet into the config file:

```
<VirtualHost *:80>
ServerName myhelpdesk.com
DocumentRoot /var/www/html/osTicket/upload

<Directory /var/www/html/osTicket>
AllowOverride All
</Directory>

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

Notice that the `ServerName` is set to `myhelpdesk.com`. This is the domain name we set and will allow us to access the ticketing system through port 80 (HTTP). After saving the file, we then need to enable this config by running

`sudo a2enmod rewrite`

`sudo a2ensite osticket.conf`

Restart Apache to finalize the changes.

`sudo systemctl restart apache2`

Now, ff we try to connect to `myhelpdesk.com` through our web browser, we'll get an error indicating the site can't be reached. This is because we still need to map the IP address of our machine to this domain name. To find your IP address, run

`ip address`

<img src="/assets/img/osticketprereqs/IP_Address.png" alt="image" height="60%" width="60%" />

Then navigate to the `/etc` directory and open the `hosts` file with sudo permissions with a text editor. We can then map our IP address to our domain name as follows:

<img src="/assets/img/osticketprereqs/hosts_file.png" alt="image" height="60%" width="60%" />

We are now able to access osTicket through our custom domain name!


<h3>Part 9 - Configuring osTicket</h3>

Open a web browser and visit `myhelpdesk.com` to begin setting up osTicket. You be greeted with the following page:

<img src="/assets/img/osticketprereqs/osticket_setup_page.png" alt="image" />

Notice that we have all the PHP features ready to use since we installed all the necessary dependencies in `Part 5`. Click `Continue` at the bottom to proceed to the basic installation page. For system settings, feel free to enter what you wish.

<img src="/assets/img/osticketprereqs/osticket_setup_system_settings.png" alt="image" height="60%" width="60%" />

When creating the admin user, we will set `Password1` as the password.

<img src="/assets/img/osticketprereqs/osticket_setup_admin_user.png" alt="image" height="60%" width="60%" />

Under the database settings, make sure to put `osticket` as the database name, since this was the name of the database we created in `Part 6`. We will set for password field to `Password1` as that was the password we when creating the database in `Part 6`.

<img src="/assets/img/osticketprereqs/osticket_setup_database_settings.png" alt="image" height="60%" width="60%" />

Click `Install Now` to complete the configuration of osTicket. Upon completion, you will be greeted with this page indicating a successful installation.

<img src="/assets/img/osticketprereqs/osticket_completed_installation.png" alt="image" />

As our final step, we need to change the file permissions of of osticket config file as indicated above. Note that `chmod 0644` is equivalent to `chmod 644`. The '0' indicates octal notation.

<img src="/assets/img/osticketprereqs/Change_Permissions_ostconfig.png" alt="image" height="60%" width="60%" />

Congratulations! You have now installed osTicket! For convenience, you can bookmark the osTicket URLs are the bottom of the installation completion page.
