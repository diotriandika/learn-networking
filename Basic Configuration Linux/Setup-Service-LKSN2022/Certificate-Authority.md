# Langkah - Langkah Membuat Self Signed Certificate Authority
Certificate Authority (CA) merupakan sebuah institusi yang mengeluarkan Digital Certificate yang mana nantinya ini digunakan untuk memvalidasi sebuah identitas (seperti webserver, email, organisasai, atau individu) yang ada di internet. CA Publik biasanya digunakan untuk mevalidasi webserver, dan CA Private digunakan dalam skala kecil atau hanya untuk area lokal.

Disini kita akan membuat sebuah CA Private yang nantinya akan kita gunakan sebagai sarana untuk mevalidasi Web-Server dan Mail-Server.

- Dalam Mode Superuser
## Membuat Direktori Root CA
Pertama kita akan membuat direktori untuk menyimpat CA Root dan masuk ke direktori tersebut
```
/# cd ~
~# mkdir /cert
~# cd cert/
```
> Quick Explaination : Untuk mengajukan request agar ditandatangani oleh CA kita membutuhkan 2 file yaitu `private key` dan file request yang disebut dengan Certificate Singning Request (CSR). `Private key` adalah sebuah `encrypted key` yang hanya boleh diakses oleh pemilik `key` itu sendiri sedangkan `CSR` adalah sebuah file atau dokumen permintaan kepada CA yang nantinya akan ditandatangani oleh CA.
## Membuat Private Key
Sekarang kita buat file `private key` terlebih dahulu. Untuk nama keynya bisa bebas
```
# openssl genrsa -out <your-key>.key 4096
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e54b20ef-620e-40cc-992b-655cb98f4285)

Selanjutnya kita edit `country code` dan `organization` pada line `134` dan `144`
```
nano -l /etc/ssl/openssl.cnf
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/acb68d71-b4e3-4d7c-b340-1c5401f446dc)
## Membuat CSR Key
Sekarang kita buat file CSR. Untuk nama sama seperti sebelumnya bisa bebas.
```
openssl req -new -key <private-key>.key -out <csr-key-name>.csr
```
> `private-key>` daalah nama dari private key yang sudah kita buat sebelumnya diatas
Akan keluar output seperti dibawah, ikuti saja dan isi sesuai dengan keinginan

> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/491447b6-9ff9-4f4e-b060-f60e9ad06630)

Untuk memastikan apakah file private key dan csr sudah dibuat kita ceke menggunakan ls
``
ls
``
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/0b760683-82aa-4791-9761-035462dbe8b3)
## Membuat Certificate Authority
Setelah membuat file csr dan private key, sekarang kita akan membuat file Certificate Authority. Kita buat dulu file dan folder yang diperlukan oleh CA.

Disini kita buat folder untuk CA dengan nama bebas, kemudian masuk ke direktori tersebut
```
# mkdir <folder-name>
# cd <folder-name>
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a6845b70-6c31-4897-b81b-52bc6272585e)

Lalu kita buat file dan folder yang dibutuhkan oleh CA di direktori tersebut
```
# mkdir certs newcerts crl
# touch index.html
# echo 0000 > serial
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e22eddd4-2346-465d-92b2-da92e61b3237)

Sekarang kita akan membuat `private key` yang dibutuhkan oleh file config CA nantinya. Nama dari `private key` bebas.
```
openssl genrsa -out <key-ca-name>.key 4096
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/55e41c9c-63b5-426c-a8b9-b2f5d2f541d4)
## Membuat CA Root
Kita buat CA Root. Disini namanya juga bebas
```
openssl req -x509 -new -key <ca-key>.key -out <root-ca-name>.crt -days 7200
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/96002976-d283-4200-a64c-579d53e83274)

> Note : `-days 7200` mengindikasikan waktu validasi dari certificate tersebut. disini berarti certificate root akan aktif/valid selama 7200 hari.
 
Semua yang dibutuhkan oleh file CA sudah lengkap. Jadi sekarang kita akan membuat sebuah file config CA yang nantinya digunakan untuk menandatangani file csr yang di ajukan oleh client.
## Konfigurasi Certificate Authority
Masuk ke direktori awal
```
cd /cert
```
Untuk membuat file config CA kita akan menyalin dari `openssl.cnf` lalu edit file tersebut
```
# cp /etc/ssl/openssl.cnf <ca-config-name>.conf
# nano <ca-config-name>.conf
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/0c4cc25b-0fb8-4d02-a032-4eb2e9f6ea50)

Edit path direktori pada line `48` (masukan direktori dari semua file CA tadi), masukan nama root certificate CA pada line `56`, masukan nama dari private key CA pada line `61`, tambahkan default md dari public key di line `80`, dan set policy di line `86`. 
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/33cf01f3-1336-4522-9c26-691fecc7c1b9)
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/dcc7071f-1359-4890-8de6-3911df0d6d86)

> Penjelasan singkat :
> - dir = direktori/folder penyimpanan semua file CA. Karena tadi saya membuatnya di direktori /CA, jadi direktori tersebut yang saya tambahkan.
> - certificate = nama CA root yang sudah dibuat. Tadi saya membuatnya dengan nama LKSN2022-Root.crt.
> - private_key = nama private key untuk CA rootnya, yang mana tadi saya membuat dengan nama CA.key.
> - default_md = sha256 artinya kita menggunakan encryption yang lebih kuat dari sha1 karena beberapa browser tidak support sha1.
> - policy = ganti policy_match menjadi policy_anything.
## Menandatangani CSR
Sekarang kita akan menandatangani file csr yang sudah diajukan oleh client. Jika diajukan pertanyaan enter `Y` saja
```
openssl ca -config <ca-config>.conf -in <csr-key>.csr
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/04d2cb4e-54b2-4922-98ea-7cdbe61349bf)

Jika keluar output seperti diatas, ini menandakan bahwa penandatanganan sertifikat berhasil

Setelah melakukan penandatanganan, akan muncul sertifikat baru yang sudah ditanda tangani oleh CA, sertifikat tersebut berdada dalam direktori `<ca-directory>/newcert` kita bisa cek dengan list di ditektori tersebut
```
ls <ca-directory>/newcert
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/0525d8b1-f40f-4b69-869a-d0b53fddefff)

Untuk memudahkan dalam navigasi dan mengingat sertifikat tersebut, kita ubah namanya sesuai dengan keinginan dan pindahkan ke direktori yang dituju
```
mv <ca-directory>/newcert/00.pem client.crt
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/2434aba6-b02e-44ef-9282-70eb11ae31d6)

## Cara menggunakan Certificate
http://trijanuari33.blogspot.com/2018/04/cara-lengkap-mengkonfigurasi.html

https://medium.com/prastamaha/konfigurasi-root-ca-intermediate-ca-server-certificate-f7484dc2be24


CARI CARA BUAT INTERMEDIATE CA





`easy-rsa` merupakan Certificate Authority management tool yang akan kita gunakan untuk generate public key dan public root certificate yang mana keduanya digunakan untuk menandatangani permintaan dari client dan server yang berjalan dengan CA kita.
```
sudo apt install easy-rsa -y
```
## Menyiapkan Direktori Public Key Infrastructure (PKI)
