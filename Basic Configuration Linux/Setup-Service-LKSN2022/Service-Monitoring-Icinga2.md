# Langkah - Langkah Instalasi dan Konfigurasi Icinga2
Icinga2 merupakan sebuah software monitoring sistem, server, resources dll berbasis web. Icinga2 bisa diguanakan dalam environment windows ataupun linux. Icinga2 juga memiliki banyak plugin-plugin yang berguna, yang salah satunya kita bisa mengirimkan notifikasi monitoring ke email.

Prerequisites:
- Terkonfigurasi IP Address pada node
- Terpasang LAMP Stack, cek [disini](https://github.com/diotriandika/learn-networking/blob/849328b9402dbc9b4b4770dca157e1bc6bb5f6aa/Basic%20Configuration%20Linux/Setup-Service-LKSN2022/LAMP-Stack.md) jika belum.
## Instalasi Icinga2
Update package dan install `icinga2` `monitoring-plugins` `apt-transport-https` 
```
sudo apt install icinga2 monitoring-plugins apt-transport-https -y
```
Enable `icinga2` ketika boot
```
sudo systemctl enable icinga2
```
Install module PHP yang diperlukan
```
sudo apt install php-gd php-pgsql php-mysql php-mbstring php-cli
```
or (optional
```
sudo apt install php-gd php-mbstring php-mysqlnd php-curl php-xml php-cli php-soap php-intl php-xmlrpc php-zip  php-common php-opcache php-gmp php-imagick php-pgsql
```
Selanjutnya edit file `/etc/php/7.4/apache2/php.ini` uncomment `date.timezone` dan set ke `"Asia/Makassar"` (gunakan CTRL + W untuk search text di editor `nano`)
```
sudo nano /etc/php/7.4/apache2/php.ini
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/66db24f0-f3c6-455b-bee2-c196d83335b0)

## Install Icinga2 IDO MySQL Module
**Icinga IDO (Icinga Data Output)** adalah fitur yang dapat mengekspor semua konfigurasi dan informasi status kedalam databse IDO. Database IDO berada di backend untuk Icinga Web.

Install Icinga IDO MySQL module
```
sudo apt install icinga2-ido-mysql -y
```
Akan muncul menu seperti dibawah, kita pilih `Yes` agar icinga2 secara otomatis mengkonfigurasi database `icinga2-ido-mysql` dengan `dbconfig-common`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/91653285-a659-4c0d-af90-a68a950b0c04)

Jika sudah kita akan diminta membuat password yang nantinya akan digunakan untuk akses && regist database `icinga2-ido-mysql` dan menu selanjutnya lagi konfirmasi password sebelumnya
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a135359c-2715-4367-9b65-c8737e36ba54)
>
>![image](https://github.com/diotriandika/learn-networking/assets/109568349/02fdfbba-be06-4c71-a508-ad40420beaf6)

Selanjutnya akan keluar window seperti berikut, kita aktifkan fitur dari icinga2 ido-mysql dengan memilih `Yes`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/214dd8a6-c732-4228-9a20-dbded9027f6a)
## Membuat Database untuk Icinga-IDO MySQL Module
Sekarang kita akan membuat database untuk icinga2-ido-mysql

Masuk ke MySQL
```
sudo mysql -u root -p
```
lalu ketikan command dibawah
```
create database <icinga-database-name>;
grant all on <icinga-database-name>.* to '<icinga-database-user>'@'localhost' identified by '<icinga-database-user-password>';
flush privileges;
quit
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/2bccf1b1-eee5-4112-a034-4a6825eb7064)

Selanjutnya kita import Icinga2 IDO schema.
```
sudo mysql -u root -p icinga_db < /usr/share/icinga2-ido-mysql/schema/mysql.sql
```

## Mengaktifkan Icinga-IDO MySQL Module
Icinga IDO MySQL memiliki konfigurasi default yang bernama `ido-mysql.conf`. Dalam file konfigurasi ini kita akan mengubah beberapa konfigurasinya untuk memberikan akses konieksi ke database yang sudah kita buat sebelumnya.
```
sudo nano /etc/icinga2/features-available/ido-mysql.conf
```
Edit file sesuai ketentuan berikut.
- set `user` dengan user yang sudah dibuat sebelumnya
- set `password` dengan password dari user
- set `host` ini biarkan default karena kita menjalankan di host lokal
- set `database` dengan nama database yang sudah kita buat sebelumnya
```
library "db_ido_mysql"

object IdoMysqlConnection "ido-mysql" {
  user = "<icinga-database-user>",
  password = "<icinga-database-user-password>",
  host = "localhost",
  database = "<icinga-database-name>" 
}
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/0e4515c5-7112-4250-b407-9fc2af14a171)

Selanjutnya aktifkan file konfigurasi tadi dengan menjalankan command
```
sudo icinga2 feature enable ido-mysql
```
Resatart service Icinga2
```
sudo systemctl restart icinga2
```
## Instal Package Icinga Web 2
Sekarang kita akan menginstal IcingaWeb2 yang mana nantinya ini akan menjadi web interface dari icinga2
```
sudo apt install icingaweb2 icingacli
```
## Membuat Database Icinga Web 2
Untuk membuat database yang diperlukan sebagai backend dari web Icinga2, login ke MySQL kalian sebagai root
```
sudo mysql -u root -p
```
Selanjutnya kita buat databasenya
```
create database <icingaweb-database-name>;
grant all on <icingaweb-database-name>.* to '<icingaweb-database-user>'@'localhost' identified by '<icingaweb-database-user-password>';
flush privileges;
quit
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/151535b5-513f-46cc-8355-9469f118f68b)

Jika sudah selanjutnya kita akan konfigurasi Icinga Web 2 nya melalui Browser
## Complete Installation & Configuration Icinga Web 2
> Note : Baca dari atas ke bawah (Teks diatas gambar ditunjukan untuk gambar dibawah teks tersebut)

Masuk ke setup Icinga Web 2 dengan mengetikan IP/domain dari server kalian ke browser 
```
http://<your-server-ip-or-domain/icingaweb2/setup
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/1bcdb2b2-4101-4bbb-be5c-a6334417e3ff)

Nah, disini kita diminta untuk memasukan token. Untuk mendapatkan token, kita generate dengan menjalankan command
```
sudo icingacli setup token create
```
> Jika kalian kehilangan setup token, bisa lihat kembali dengan mengubah argument `create` menjadi `show`
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/4d2cf67a-4a85-4083-901c-855dbb5df08c)

Jika sudah masukan token dan select next.

Dimenu selanjutnya kita bisa mengaktifkan beberapa module bawaan, namun disini saya biarkan default saja.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d42a81c3-5817-4e16-9dc5-595c939d1ceb)

Menu berikutnya adalah list darimodule php yang aktif, select next untuk melanjutkan
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/acf43179-ce23-4792-8e67-b6e4e6ed7e8a)

Dimenu ini kita bisa customize backend untuk akses Icinga Web, karena sebelumnya kita sudah membuat database untuk backend, kita akan menggunakan authetication type `Database`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/58ef5192-e47f-4b8b-bd00-1c581badf141)

Selanjutnya kita akan mengalokasikan database dari icingaweb2 yang sebelumnya sudah kita buat. 
- isi `Database Name` dengan nama database icingaweb2 kalian
- isi `Username` dengan user yang sudah kita buat di database icingaweb2
- isi `Password` dengan password dari user database icingaweb2
Lalu validasi konfigurasi dengan klik menu `Validate Configuration` di kanan `Next`, jika muncul notifikasi hijau diatas, ini menandakan bahwa konfigurasi sudah tervalidasi.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/0b3a7340-c445-4b72-9245-d1f7bc645df6)

Untuk Authentication Backend opsional, ini karena kita sudah menggunakan database untuk autentikasi. Kita bisa mengubah nama dari backendnya, namun disini saya biarkan default.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/1ad317ed-0968-4580-a31d-9e6b9fdf9864)

Sekarang kita akan membuat akun yang bersifat Administrative. Kalian isi saja sesuai keingginan, namun perlu diperhatikan untuk mengingat User dan Passwordnya. Karena nanti diperlukan saat autentikasi login pertama.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/c69a76be-68f3-435a-872b-78c131218aa7)

Di menu Application Configuration kita bisa melakukan kustomisasi mulai dari Storage Type, Logging Type, dan facility. Disini saya biarkan default karena tidak ada yang perlu saya rubah lagi
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/bfa03629-21ce-4f90-b898-8f2de1ba17a7)

Validasi konfigurasi kembali, jika merasa sudah benar bisa langsung next dimenu ini.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/cfb64b74-fe9f-45e8-aede-17469d36c612)

Sekarang kita akan konfigurasi monitoring module untuk Icinga Web 2, disini next saja.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/8b64e581-033b-4824-b093-b1a1399bf034)

Menu monitoring backend kita select backend typenya IDO, sesuai dengan backend yang sudah dikonfigurasi sebelumnya untuk icinga ido 
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d9bcf058-1501-436d-9fc8-2ed562e094c6)

Selanjutnya sama seperti sebelumnya, kita alokasikan database icinga ido
- isi `Database Name` dengan nama database icinga ido kalian
- isi `Username` dengan user yang sudah kita buat di database icinga ido
- isi `Password` dengan password dari user database icinga ido
Lalu validasi konfigurasi dengan klik menu `Validate Configuration` di kanan `Next`, jika muncul notifikasi hijau diatas, ini menandakan bahwa konfigurasi sudah tervalidasi.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/938de2e2-e56b-47db-991c-2966a44f1310)

Di menu Command Transport, rubah Transport Type menjadi Local Command File sebagai jalur untuk mengirim command ke instansi monitoring kita.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/eaaec071-91af-4ba4-94a4-dec3763abf35)
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/bd39defd-92f8-4bd3-b299-8c7c0bb75404)

Terakhir menu Monitoring Security bisa dibiarkan default dan klik `Next`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/22b6b1ec-ea43-43d9-ac31-f3acaf072a38)

Validasi konfigurasi kembali, jika merasa sudah benar select `Finish`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/b6b22ec0-a394-41bb-b7f3-432ed84abab4)

Jika sudah, sekarang kita seharusnya sudah bisa login ke Icinga Web 2 dengan mengklik `Login to Icinga Web 2`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/0f43e188-8b48-4631-853c-9a657bd1d090)
## Verifikasi
Login dengan Administrative Account yang sudah dibuat sebelumnya dan login
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/9b2dfbe4-54a8-4a10-96e7-16a668ce38f5)

Jika muncul page seperti ini, ini menandakan icinga2 berhasil terinstall dan terkonfigurasi
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d3eec620-9d37-4ddb-874c-c2f4a1c207d1)

