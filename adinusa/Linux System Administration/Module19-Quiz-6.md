# Objective
![image](https://github.com/diotriandika/learn-networking/assets/109568349/ba862923-f317-489f-b51a-f86281775204)
## Penyelesaian Task
1. Basic Troubleshooting Apache
   - start apache2, maka akan muncul error. nah untuk melihat lognya bisa cek melalui `systemctl status apache2`
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/f4b054f3-2ed8-4496-8040-cef6625eb114)
   - yang pertama disini terdapat error `AH00526: Syntax error on line 148 of /etc/apache2/apache2.conf:` ini berarti ada masalah pada line 148 do apache2.conf
   - cek file dengan nano dan pergi ke line 148
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d585f2d9-3583-4807-b350-be556c2aa3c6)
   - disini terdapat line SSLEngine, namun pada log 2 disebutkan
     ```
     Invalid command 'SSLEngine', perhaps misspelled or defined by a module not included in the server configuration
     ```
   - ini menandakan modul SSLEngine belum dipasang
   - aktifkan dengan syntax `a2enmod ssl` kemudian restart apache2
   - source : https://hoststud.com/resources/how-to-fix-the-invalid-command-sslengine.1148/
   - disini masih terdapat error kembali, disini log 2 disebutkan line 148 bersifat ambigu
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e0a239fa-b246-4de2-8dda-83a562765c45)
   - coba aktifkan SSLEngine dengan edit di `apache2.conf` pada line 148 dengan menambahkan argument `SSLEngine on`
   - restart apache2. disini masih terdapat error sebagai berikut
     ```
     AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 192.168.0.11. Set the 'ServerName' directive globally to suppress this message
     ```
   - ini menandakan XXX NOT SOLVED LOG :
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/fc924495-06bc-4e44-a69f-a535f7e5b45e)
## Kemungkinan ini karena belum ada Certificate SSL. next coba lagi, buat sekarang bisa disable aja SSLEnginenya
   - disable SSLengine di apache2.conf `SSLEngine off`
   - Restart Apache2, dan kembali work cuma Certificate Authority ilang kyknya.
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/8156d603-17c1-4f80-beb8-597b94c13969)
## jangan lupa untuk grant access ke /var/www di apache2.conf

![image](https://github.com/diotriandika/learn-networking/assets/109568349/5c3dfaff-4e8c-4426-b297-0314753aa1e1)
# **_Task nearly complete :(_**
