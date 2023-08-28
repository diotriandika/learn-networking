# Langkah - Langkah Instalasi dan Konfigurasi Apache2
Apache merupakan sebuah program yang menyediakan layanan website.

Topologi yang saya gunakan :
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/758fde41-5a77-46f2-9c89-21d3c94bccdf)

## Instalasi Apache2
Update local package kemudian install apache2

     sudo apt update && sudo apt install apache2 ufw -y

Atur firewall menggunakan tool `ufw` atau `firewalld`, disini kita menggunakan ufw dan allow pada port 80 untuk http dan 443 untuk https

     sudo ufw allow 80
     sudo ufw allow 443

Selanjutnya kita cek apakah default page apache sudah bisa dibuka atau belum dengan mengetikan IP Address pada search bar browser client
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/40f7560e-c0ac-4c12-9d44-69e2ef678571)

Jika muncul seperti ini berarti default page apache sudah berhasil diakses

## Konfigurasi Web Apache2
Untuk merubah default web dari apache menjadi web yang kita inginkan ikuti langkah langkah dibawah.

Masuk ke direktori `/etc/apache2/sites-available/`
```
cd /etc/apache2/sites-available
```
List file yang ada di direktori tersebut
```
ls
```
![image](https://github.com/diotriandika/learn-networking/assets/109568349/67e399f0-f115-4e79-9b0a-5bbe51d16d42)

Disini terdapat dua file, `yaitu 000-default.conf` dan `default-ssl.conf`
> Note : `000-default.conf` adalah file sites default dari apache, dan `default-ssl.conf` adalah file sites secure default dari apache. Untuk saat ini saya hanya akan menggunakan `000-default.conf` saja karena saya tidak membutuhkan sites secure/https yang dimana akan saya bahas dilain hari sekaligus dengan penjelasan mengenai penggunaan Certificate Authority.

Lanjut, copy file 000-default.conf dengan nama `<your-site>.conf`
```
sudo cp 000-default.conf <your-site>.conf
```
Contoh :
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ef753383-3f88-41b1-8207-9e1bd968e2d3)

Edit file yang sudah dibuat tadi
```
sudo nano <your-site>.conf
```
Edit `DocumentRoot` dengan path dari lokasi file `index.html` yang akan kita buat nantinya

Contoh :
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/0e24834f-0183-4014-b5f8-d442612f797a)

Save dengan CTRL+X dan Y kemudian ENTER

## Membuat Site Apache2
Selanjutnya kita masuk ke direktori `/var/www` 
```
cd /var/www
```
dan buat folder baru dengan `mkdir` sesuai dengan path yang sudah dibuat tadi di `<your-site>.conf`
```
sudo mkdir <your-directory-name>
```
Contoh :
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ba2742ad-5c21-4a41-becb-d787b37ef72c)

### Membuat file index.html
`index.html` adalah site yang akan digunakan nantinya, secara default apache akan membaca file ini. Disini kita bisa membuat file `index.html` dengan dua cara yaitu, mengcopy file original apache atau bisa juga kita buat file baru
#### _Membuat index.html dengan mengcopy file asli_
Copy file `index.html` yang ada di direktori `html` ke direktori yang sudah kita buat tadi.
```
sudo cp html/index.html <your-directory-name>/index.html
```
Kemudian masuk ke direktori tersebut.
```
cd <your-directory-name>
```
Contoh:
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/1adc011c-8ee2-42c5-aeb3-3759c879e0a7)

Jika sudah, edit file `index.html`
```
sudo nano index.html
```
Selanjutnya isi dengan code html sesuai dengan keinginan kalian, atau bisa edit code yang sudah ada.
#### _Membuat file index.html baru_
Untuk membuat file index.html, kita bisa menggunakan tool `touch` atau bisa langsung membuat sekaligus edit file dengan `nano`, maka akan secara otomatis membuat file index.html. Disini saya akan langsung membuat file index.html langsung dengan text editor `nano`
```
sudo nano index.html
```
Jika sudah, sama seperti diatas kita isi dengan code html

Contoh :
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/651542ce-8aaa-4d56-bdd0-e43aab7982ec)

Save dengan CTRL+X dan Y kemudian ENTER

## Mengaktifkan Site Apache2
Terakhir kita akan mengaktifkan site yang sudah kita buat barusan, sebelum itu kita akan menonaktifkan site default yang masih berjalan dengan command
```
sudo a2dissite 000-default.conf
```
kemudian mengaktifkan site kita
```
sudo a2ensite <your-site>.conf
```
Restart apache2 dengan command
```
sudo systemctl restart apache2.service
```
Contoh :
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/87311861-d676-4cdc-aff3-409c61dbcf27)

## Verifikasi
Cek status apache2 apakah sudah berjalan dengan normal atau belum
```
sudo systmctl status apache2
```
Jika sudah cek kembali dibrowser client dengan mengetikan IP Address Server pada tab search
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/7ce026c9-4510-40ae-94b7-ba9e614c5376)

Jika sudah berubah ini berarti site kita sudah bisa berjalan.

**_Done_**
