# Langkah - Langkah Instalasi Roundcube Webmail
![roundcube-logo-1024x538](https://github.com/diotriandika/learn-networking/assets/109568349/3c53cc6b-22b1-43bb-8a4e-7607938abc5b)

Roundcube merupakan sebuah email client IMAP yang berbasis Web atau bisa disebut Webmail yang digunakan untuk mengelola email. Karena Roundcube dioperasikan melalui Website, jadi pengoperasiannya mudah untuk mayoritas orang.

- Pastikan sudah mengkonfigurasi [Postfix](https://github.com/diotriandika/learn-networking/blob/3f579774a837c60da92bcb3da6aed09d1967bdd3/Basic%20Configuration%20Linux/Setup-Service-LKSN2022/Postfix-SMTP-Server.md) dan [Dovecot](https://github.com/diotriandika/learn-networking/blob/6a6cfd1a5342b826d3e62469d0584ba5e9f5d1d5/Basic%20Configuration%20Linux/Setup-Service-LKSN2022/Dovecot-IMAPD%26POP3.md).
- Project ini terhubung dengan project sebelumnya agar mudah untuk mengerti fungsi dari setiap programnya.

## Instalasi Roundcube
Instal `mariadb-server` sebagai database untuk Roundcube dan rouncube itu sendiri.
```
sudo apt install mariadb-server roundcube
```
Untuk menu dibawah, select `yes` untuk membuat database secara otomatis oleh roundcube.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ca106721-bd8c-4837-acb1-b95dbbf2ffbe)

Selanjutnya masukan password yang akan digunakan sebagai password database roundcube dan confirm password

## Konfigurasi Roundcube
Edit file konfigurasi `config.inc.php`
```
sudo nano -l /etc/roundcube/config.inc.php
```
Pada line `36` tambahkan domain dari mail server yang akan digunakan
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/7397f093-df2e-4f17-bfeb-fc617e95ff63)

Diline `48` ganti `'smtp_server'` dengan domain dari mail server, pada line `51` ganti `'smtp_port'` menjadi `22` dan kosongkan value dari `'smtp_user'` dan `'smtp_password'` pada line `55` dan `59`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/c136cb68-cf2a-46cb-b181-e079bd249130)

Sekarang configure ulang roundcube
```
sudo dpkg-reconfigure roundcube-core
```
Untuk menu dibawah kita kosognkan saja, karena kita disini kita tidak menggunakan tls/ssl
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/26f57481-94b5-4792-83f0-c440e0748b5d)

Selanjutnya pilih bahasa, dan menu reinstall pilih `No` jika tidak ingin reinstall database yang sudah dibuat sebelumnya
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a287ed53-161b-4f0c-a7fd-9add76f864be)

Menu selanjutnya adalah memilih software webservice untuk menampilkan Webmail roundcube, karena kita memerlukan webserver sebagai perantara. Disini saya hanya memilih apache2 dan kemudian select `yes` pada menu restart webserver.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/11401c7d-7d69-4c71-aa01-0e5f2cd404cf)
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/c04dacb1-4e9c-40ec-bf20-20ab67e5a552)

Terakhir pilih `keep the local version currently installed` jika tidak ingin mengubah versi roundcube.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/fa069ef5-4f96-4f2e-aea8-f51120f255aa)

## Konfigurasi Apache2 sebagai Webserver untuk Roundcube
Edit file `apache2.conf` dan tambahkan `Include /etc/roundcube/apache.conf` pada line paling akhir
```
sudo nano /etc/apache2/apache2.conf
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/962fce68-32bf-4846-925d-26daaad3a7f6)

Selanjutnya masuk ke direktori `/etc/apache2/sites-available/` kemudian buat file yang nantinya digunakan sebagai aktivasi site roundcube
```
$ cd /etc/apache2/sites-available
$ sudo touch <your-site-name>.conf
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/00338fa8-6165-494f-b47f-8493fdebcd47)

Edit file `<your-site-name>.conf` dan isi dengan input berikut
```
sudo nano <your-site-name>.conf
```
```
<VirtualHost *:80>
    ServerName <your-domain-name>
    DocumentRoot /usr/share/roundcube
</VirtualHost>
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/2f579add-a75c-49de-a71d-74d04242295a)

Disable site default dan enable site yang sudah dibuat barusan kemudian restart service apache2
```
$ sudo a2dissite 000-default.conf
$ sudo a2ensite <your-site-name>.conf
$ sudo systemctl restart apache2
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a09130bd-9c34-46b7-9ae5-71408770c224)

## Verifikasi
Akses domain mail server di browser client, pastikan DNS Mail server sudah diset di client tersebut.

Jika sudah maka akan muncul tampilan login, disini kita bisa masukan user yang sudah dibuat. Contohnya saya mau melihat email yang sebelumnya saya kirim menggunakan user `zeta` ke `moona`.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/1771fcf0-3bdc-4d31-afef-73936ca93653)

Nah disini sudah terlihat email yang sebelumnya sudah kita kirim melalui user `zeta` dan bisa dilihat isinya juga sama
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/8b4baed0-add3-4bd4-9192-5f12e4a02513)
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/69715606-c80d-4408-a316-70c0513f46b6)

Kita juga bisa mengirim pesan kembali melalui Webmail ini, sebagai contoh saya mengirimkan email ke `zeta@itnsa.id` dari user `moona`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/7b5893a2-daff-4fc0-a685-ebe5b0c5d644)
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a261c3b4-b2b0-4b9f-ad82-a2eee4d94ffb)


> Note : Jika misalnya kalian tidak bisa mengakses websitenya, allow port 80 dengan `ufw` atau tools firewall lainnya, begitu juga dengan jalur udp yaitu 53, port 23, serta 110
>
> Contoh
> ```
> ufw allow 80
> ufw allow 35
> ufw allow 23
> ufw allow 110
> ufw reload
> ufw resatrt

**_Done_**

