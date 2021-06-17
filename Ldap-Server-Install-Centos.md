#edit vi /etc/hosts
vi /etc/hosts

#Add line 192.168.1.150 amol.domain.com server    (IP servername)


#if firewall on 
#firewall-cmd --permanent --add-port=389/tcp
#firewall-cmd --permanent --add-port=636/tcp
#firewall-cmd --permanent --add-port=9830/tcp
#firewall-cmd --reload


#update yum repo 
#yum install epel-release




#not required 
vi /etc/sysctl.conf
Add the following lines:

net.ipv4.tcp_keepalive_time = 300
net.ipv4.ip_local_port_range = 1024 65000
fs.file-max = 64000

#Verify the settings:
sysctl -p


#not required
Edit /etc/security/limits.conf file:

vi /etc/security/limits.conf
Add the following lines at the bottom:

*               soft     nofile          8192   
*               hard     nofile          8192



#not required
Edit /etc/profile file:

vi /etc/profile
Add the following line:

ulimit -n 8192


#not required
Edit /etc/pam.d/login file:

vi /etc/pam.d/login
Add the following line at end:

session    required     /lib/security/pam_limits.so




#Create a LDAP user account

useradd ldapuser
passwd ldapuser

#Install LDAP server

yum install 389-ds-base 389-admin

#Configure 389 Directory server

setup-ds-admin.pl





Sample output:

==============================================================================
This program will set up the 389 Directory and Administration Servers.

It is recommended that you have "root" privilege to set up the software.
Tips for using this program:
 - Press "Enter" to choose the default and go to the next screen
 - Type "Control-B" then "Enter" to go back to the previous screen
 - Type "Control-C" to cancel the setup program

Would you like to continue with set up? [yes]: ## Press Enter

==============================================================================
Your system has been scanned for potential problems, missing patches,
etc. The following output is a report of the items found that need to
be addressed before running this software in a production
environment.

389 Directory Server system tuning analysis version 23-FEBRUARY-2012.

NOTICE : System is x86_64-unknown-linux3.10.0-327.22.2.el7.x86_64 (1 processor).

Would you like to continue? [yes]: ## Press Enter

==============================================================================
Choose a setup type:

 1. Express
 Allows you to quickly set up the servers using the most
 common options and pre-defined defaults. Useful for quick
 evaluation of the products.

 2. Typical
 Allows you to specify common defaults and options.

 3. Custom
 Allows you to specify more advanced options. This is 
 recommended for experienced server administrators only.

To accept the default shown in brackets, press the Enter key.

Choose a setup type [2]: ## Press ENTER

==============================================================================
Enter the fully qualified domain name of the computer
on which you're setting up server software. Using the form
<hostname>.<domainname>
Example: eros.example.com.

To accept the default shown in brackets, press the Enter key.

Warning: This step may take a few minutes if your DNS servers
can not be reached or if DNS is not configured correctly. If
you would rather not wait, hit Ctrl-C and run this program again
with the following command line option to specify the hostname:

 General.FullMachineName=your.hostname.domain.name

Computer name [server.ostechnix.lan]: ## Press ENTER

==============================================================================
The servers must run as a specific user in a specific group.
It is strongly recommended that this user should have no privileges
on the computer (i.e. a non-root user). The setup procedure
will give this user/group some permissions in specific paths/files
to perform server-specific operations.

If you have not yet created a user and group for the servers,
create this user and group using your native operating
system utilities.

System User [nobody]: ldapuser ## Enter Ldap username which we created earlier
System Group [nobody]: ldapuser ## Enter LDAP group name (It is same as username)

==============================================================================
Server information is stored in the configuration directory server.
This information is used by the console and administration server to
configure and manage your servers. If you have already set up a
configuration directory server, you should register any servers you
set up or create with the configuration server. To do so, the
following information about the configuration server is required: the
fully qualified host name of the form
<hostname>.<domainname>(e.g. hostname.example.com), the port number
(default 389), the suffix, the DN and password of a user having
permission to write the configuration information, usually the
configuration directory administrator, and if you are using security
(TLS/SSL). If you are using TLS/SSL, specify the TLS/SSL (LDAPS) port
number (default 636) instead of the regular LDAP port number, and
provide the CA certificate (in PEM/ASCII format).

If you do not yet have a configuration directory server, enter 'No' to
be prompted to set up one.

Do you want to register this software with an existing
configuration directory server? [no]: ## Press ENTER

==============================================================================
Please enter the administrator ID for the configuration directory
server. This is the ID typically used to log in to the console. You
will also be prompted for the password.

Configuration directory server
administrator ID [admin]: ## Press ENTER
Password: ## Enter LDAP administrative user password
Password (confirm): ## Re-enter password

==============================================================================
The information stored in the configuration directory server can be
separated into different Administration Domains. If you are managing
multiple software releases at the same time, or managing information
about multiple domains, you may use the Administration Domain to keep
them separate.

If you are not using administrative domains, press Enter to select the
default. Otherwise, enter some descriptive, unique name for the
administration domain, such as the name of the organization
responsible for managing the domain.

Administration Domain [ostechnix.lan]: ## Press ENTER

==============================================================================
The standard directory server network port number is 389. However, if
you are not logged as the superuser, or port 389 is in use, the
default value will be a random unused port number greater than 1024.
If you want to use port 389, make sure that you are logged in as the
superuser, that port 389 is not in use.

Directory server network port [389]: ## Press ENTER

==============================================================================
Each instance of a directory server requires a unique identifier.
This identifier is used to name the various
instance specific files and directories in the file system,
as well as for other uses as a server instance identifier.

Directory server identifier [server]: ## Press ENTER 

==============================================================================
The suffix is the root of your directory tree. The suffix must be a valid DN.
It is recommended that you use the dc=domaincomponent suffix convention.
For example, if your domain is example.com,
you should use dc=example,dc=com for your suffix.
Setup will create this initial suffix for you,
but you may have more than one suffix.
Use the directory server utilities to create additional suffixes.

Suffix [dc=ostechnix, dc=lan]: ## Press ENTER

==============================================================================
Certain directory server operations require an administrative user.
This user is referred to as the Directory Manager and typically has a
bind Distinguished Name (DN) of cn=Directory Manager.
You will also be prompted for the password for this user. The password must
be at least 8 characters long, and contain no spaces.
Press Control-B or type the word "back", then Enter to back up and start over.

Directory Manager DN [cn=Directory Manager]: ## Press ENTER

==============================================================================
Certain directory server operations require an administrative user.
This user is referred to as the Directory Manager and typically has a
bind Distinguished Name (DN) of cn=Directory Manager.
You will also be prompted for the password for this user. The password must
be at least 8 characters long, and contain no spaces.
Press Control-B or type the word "back", then Enter to back up and start over.

Directory Manager DN [cn=Directory Manager]: ## Press ENTER
Password: ## Enter Password for directory manager
Password (confirm): ## Re-enter password

==============================================================================
The Administration Server is separate from any of your web or application
servers since it listens to a different port and access to it is
restricted.

Pick a port number between 1024 and 65535 to run your Administration
Server on. You should NOT use a port number which you plan to
run a web or application server on, rather, select a number which you
will remember and which will not be used for anything else.

Administration port [9830]: ## Press ENTER

==============================================================================
The interactive phase is complete. The script will now set up your
servers. Enter No or go Back if you want to change something.

Are you ready to set up your servers? [yes]: ## Press ENTER
Creating directory server . . .
Your new DS instance 'server' was successfully created.
Creating the configuration directory server . . .
Beginning Admin Server creation . . .
Creating Admin Server files and directories . . .
Updating adm.conf . . .
Updating admpw . . .
Registering admin server with the configuration directory server . . .
Updating adm.conf with information from configuration directory server . . .
Updating the configuration for the httpd engine . . .
Starting admin server . . .
The admin server was successfully started.
Admin server was successfully created, configured, and started.
Exiting . . .
Log file is '/tmp/setup46mxl0.log'



Test LDAP server

ldapsearch -x -b "dc=domain,dc=com"



Starting and Stopping 389-ds services
Enable directory server and directory admin  services automatically on every reboot:
systemctl enable dirsrv.target
systemctl enable dirsrv-admin
Start directory server's service:
systemctl start dirsrv.target
Or

start-dirsrv
Stop directory server's service:
systemctl stop dirsrv.target
Or

stop-dirsrv
Start directory admin:
systemctl start dirsrv-admin
Or

start-ds-admin
Stop directory admin:
systemctl stop dirsrv-admin
Or

stop-ds-admin
Check the status of the services:
systemctl status dirsrv.target
systemctl status dirsrv-admin
Restart services:
systemctl restart dirsrv.target
systemctl restart dirsrv-admin

