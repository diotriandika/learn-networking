# Langkah - Langkah Instalasi dan Konfigurasi Email Server dengan Postfix SMTP
Postfix sebagai SMTP-Server adalah sebuah MTA (Message Transfer Agent) yang menyediakan layanan untuk mengirimkian email.

- Pastikan IP Address sudah terkonfigurasi
- FQDN sudah diset
  
> Note : Sebelum instalasi Roundcube, saya rekomendasikan untuk menyiapkan alamat domain yang nantinya digunakan untuk konfigurasi. Contohnya saya disini menggunakan domain `mail.itnsa.id` yang saya konfigurasi lokal dengan bind 9
>
> Cek [disini](https://github.com/diotriandika/learn-networking/blob/6a6cfd1a5342b826d3e62469d0584ba5e9f5d1d5/Basic%20Configuration%20Linux/Assesmen%20Sistem%20Jaringan%20XI/DNS-Server.md) untuk cara set-up domain dengan bind9

## Instalasi Postfix
Update list package dengan `apt-get update` kemudian install postfix
```
sudo apt install postfix -y
```
Akan muncul menu seperti dibawah, select OK dan menu selanjutnya pilih Internet Site
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ecc1a9f3-6996-4213-822a-b1e3c34e4469)
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/5d8d2fe2-cfef-4ce4-a5c3-ef457f012d47)

Selanjutnya masukan domain name di menu dibawah, ini nantinya akan menjadi domain pada alamat e-mail nantinya. Contohnya disini saya menggunakan `itnsa.id` yang dimana nantinya akan menjadi alamat email `user@itnsa.id`.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e7545256-b924-4042-a6b6-ed22b6981dd0)

## Konfigurasi Postfix
Edit file `main.cf` untuk mengalokasikan direktori mail
```
sudo nano /etc/postfix/main.cf
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/3ae25d5c-0ff5-471b-b827-de84a592f118)

Reconfigure Postfix dengan command :
```
sudo dpkg-reconfigure postfix
```
Untuk menu awal hingga menu domain, kita samakan dengan sebelumnya. Disini kita juga bisa merubah konfigurasi yang kita lakukan sebelumnya jika dirasa ada yang salah/sekedar ingin merubah.

Untuk di menu postmaster recipient dikosongkan saja
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a8eab652-505b-4308-b14c-bafc48ebcb29)

Menu selanjutnya kita bisa menambahkan accept mail dari domain lain atau server lain. Untuk menu Force Synchronous pilih NO.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/03948aab-2b96-451c-9685-4767e24ef51a)
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/52c961e1-0779-40cf-972d-c00db65ba51c)

Menu Local Network, masukan semua Network Local yang ingin diberikan akses. Disini saya menggunakan 0.0.0.0/0 yang bertujuan untuk memberikan akses kesemua network yang terhubung oleh server saya.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e64eab8d-3027-4d66-a304-4826a2c3f3fd)

Untuk mailbox size limit, `0` disini berarti tidak dibatasi.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/1fa27acc-39a6-4711-b0c5-f4df65c7cd4d)

Menu ini yaitu memberikan ekstensi pada Local Address yang bertujuan untuk mendefiniskan setiap network. Disini kosongin aja
![image](https://github.com/diotriandika/learn-networking/assets/109568349/c4d28277-29f0-4c67-b147-fdca56244186)

Terakhir menentukan protokol IP mana yang akan digunakan. Karena saya hanya menggunakan IPv4, oleh karena itu saya memiliih opsi IPv4 lalu OK
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/3eac23b9-892c-4d6d-8ce8-ac4f9d77b1fa)

> Note : Jika ada salah konfigurasi, gunakan `dpkg-reconfigure <service>` untuk konfigurasi ulang

Terakhir Restart Service Postfix
```
sudo systemctl restart postfix
```

## Verifikasi
Untuk meverifikasi apakah Postfix sudah berjalan, pertama kita buat user terlebih dahulu sebagai testing. **(Opsional)**
```
sudo adduser <user>
```
Disini saya membuat dua user sebagai test (nantinya mona akan sebagai penerima di task berikutnya)
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e1266c47-757c-4327-a509-2019078a8614)

Mengirim pesan dengan telnet. Untuk mengirim pesan menggunakan telnet, disini menggunakan port 25
```
telnet <your-domain> 25
```
Format :
```
mail from: user_sender
rcpt to: user_receiver
data
<messages>
.
quit
```
> !![image](https://github.com/diotriandika/learn-networking/assets/109568349/c0201933-5810-49cd-84e2-05ddedaca463)

