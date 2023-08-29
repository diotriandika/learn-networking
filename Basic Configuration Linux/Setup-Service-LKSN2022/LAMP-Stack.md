# Langkah - Langkah Instalasi LAMP Stack
![image](https://github.com/diotriandika/learn-networking/assets/109568349/21269ca9-387b-40c8-a9b7-0fa253e46a28)

LAMP Stack atau `Linux Apache2 MySQL PHP` merupakan sebuah gabungan dari empat software yang digunakan oleh developer untuk membangun website atau web application. LAMP juga menciptakan environment yang reliable dan sangat maksimal.

## Instalasi LAMP 
Instalasi LAMP dengan Database-Server MariaDB.
```
apt update && apt install apache2 mariadb-server mariadb-client mariadb-common php php-mysqli
```
Selanjutnya cek apakah semua service sudah berjalan 
```
systemctl status mysql

systemctl status apache2
```
Start service dan enable ketika boot
```
systemctl enable apache2

systemctl enable mysql
```
## Mengamankan MySQL
```
mysql_secure_installation
```
Ikuti seperti dibawah
```
lnearher@MON-1:~$ sudo mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] y
Enabled successfully!
Reloading privilege tables..
 ... Success!


You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!

```

**_Done!_**
- Referensi : https://www.tecmint.com/install-lamp-in-debian/
