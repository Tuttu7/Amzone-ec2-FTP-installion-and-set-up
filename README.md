
> Install the vsftpd service in the instance via the command
```
yum install vsftpd
```
> Next, you'll need to open up the FTP ports on your EC2 server. Select the Inbound tab and add port range 20-21. Also add port range 1024-1048

```
Type            protocol port range     Source
Custom TCP Rule	TCP	     20 - 21	        0.0.0.0/0
Custom TCP Rule	TCP	    1024 - 1048	        0.0.0.0/0
```

> Make updates to the vsftpd.conf file

```
vi /etc/vsftpd/vsftpd.conf
```
> Disable anonymous FTP by changing this line:
```
anonymous_enable=YES

to 

anonymous_enable=NO
```
> Then add the following lines to the bottom of the vsftpd.conf file:
```
pasv_enable=YES
pasv_min_port=1024
pasv_max_port=1048
pasv_address=<Public IP of your instance>
local_root=/var/www/html
```

> Restaring vsftpd service
```
systemctl restart vsftpd
```
> creating FTP user
```
[root@ip-172-31-45-208 ~]# useradd ftpuser
[root@ip-172-31-45-208 ~]# id ftpuser
uid=1001(ftpuser) gid=1001(ftpuser) groups=1001(ftpuser)
[root@ip-172-31-45-208 ~]# passwd ftpuser
Changing password for user ftpuser.
New password: 
Retype new password: 
passwd: all authentication tokens updated successfully.
````

> Restricting users to their home directories
```
vi /etc/vsftpd/vsftpd.conf
chroot_local_user=YES
chroot_local_user=YES
```
> While getting the following error messages while connecting via FTP you can use the below steps to sort out this issue.

```
Error : 

$ ftp 13.233.223.197 21
Connected to 13.233.223.197.
220 (vsFTPd 3.0.2)
Name (13.233.223.197:tuttu): ftpuser
331 Please specify the password.
Password:
500 OOPS: cannot change directory:/var/www/html
Login failed.

Solution :

Set the homedirectory correctly for the user via the command:

[root@ip-172-31-45-208 html]# usermod -m -d /home/ftpuser/var/www/html ftpuser

# ftp 13.233.223.197 21
Connected to 13.233.223.197.
220 (vsFTPd 3.0.2)
Name (13.233.223.197:tuttu): ftpuser
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
226 Directory send OK.
ftp> 
```

