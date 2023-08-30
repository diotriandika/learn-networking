# Langkah - Langkah Instalasi Dovecot IMAPD dan POP3
Dovecot adalah server IMAP & POP3 yang bertugas untuk menyimpan dan meneruskan email tersebut ke penerima dengan protokol yang IMAP & POP3.

> Berikut referensi hubungan antara Postfix dengan Dovecot : https://www.proweb.co.id/articles/postfix/postfix_dovecot.html

- Pastikan sudah melakukan konfigurasi dengan Postfix seperti halnya [disini](https://github.com/diotriandika/learn-networking/blob/main/Basic%20Configuration%20Linux/Setup-Service-LKSN2022/Postfix-SMTP-Server.md)
## Instalasi Dovecot
Install Dovecot dengan IMAPD dan POP3
```
sudo apt install dovecot-imapd dovecot-pop3
```
## Konfigurasi Dovecot
Edit file konfigurasi `dovecot.conf` dan uncomment pada line ke 30
```
sudo nano -l /etc/dovecot/dovecot.conf
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/cc328b37-0009-46b7-90c1-984129f83909)

Edit file konfigurasi `10-auth.conf`, uncomment di line 10 dan ganti disable_plaintext_auth menjadi no
```
sudo nano -l /etc/dovecot/conf.d/10-auth.conf
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/24f4310b-c8fe-4b90-9461-eb509477156c)

Edit file konfigurasi `10-mail.conf`, edit `mail_location` dengan `~/Maildir`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/be1b5cd0-b59b-4a39-84ce-96dcdc30aa71)

Terakhir restart service dovecot
```
sudo systemctl restart dovecot
```
## Verifikasi
Untuk mengecek apakah mail yang kita kirim sebelumnya sudah bisa atau belum kita cek menggunakan telnet
```
telnet mail.itnsa.id 110
```
Format :
```
user <user-receiver>
pass <user-receiver-password>
list
retr <message number>
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e006762d-1aa8-4c42-ab8c-4c59b85ff22c)

Nah disini sudah bisa kita lihat isi pesan yang dikirim sebelumnya oleh `zeta@itnsa.id`
> Note : Jika tidak ada pesan masuk. Coba restart kedua service `postfix` dan `dovecot` lalu kirim pesan ulang seperti sebelumnya
