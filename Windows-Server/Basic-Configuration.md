# Konfigurasi Dasar Windows
Sebelum menggunakan Windows Server, direkomendasikan untuk melakukan beberapa konfigurasi dasar sebagai berikut
- [x] IP Addressing
- [x] Set Hostname
- [x] Sysprep (for VM)
## Konfigurasi IP 
Konfigurasi IP dengan mengetikan `View Network Connection` pada search bar atau akses melalui `Control Panel` dengan Path berikut
```bash
Control Panel > Network adn Internet > Network and Sharing Center
```
Kemudian select `Change Adapter Settings`
![image](https://github.com/diotriandika/learn-networking/assets/109568349/66a8b2a9-b974-4b7f-bb52-cd64c4bef2e4)

`Right-Click ` pada Ethernet / Adapter yang ingin digunakan dan pilih `Properties` kemudian pilih `Internet Protocol Version 4` (Jika ingin mengguakan IPv4), disini saya menggunakan Ethernet0 dengan konfigurasi dasar static IP karena akan saya gunakan sebagai Active Directory dan Domain Controller.
![image](https://github.com/diotriandika/learn-networking/assets/109568349/b9771390-88a1-4f30-9315-7d5be73c1617)

Verifikasi menggunakan `Command Prompt` dengan mengekekusi perintah `ifconfig`
![image](https://github.com/diotriandika/learn-networking/assets/109568349/50482181-4c24-46eb-8bc5-2322c1389770)
## Set Hostname
Untuk mengatur Hostname, Masuk ke Control Panel dengan path berikut
```bash
Control Panel > System and Security > System
```
Pilih `Change Settings` dikanan Computer Name
![image](https://github.com/diotriandika/learn-networking/assets/109568349/7542e7f3-d882-4ac7-a1d5-a8e306e197d9)

Pilih `Change` dan isi hostname pada kolom Computer Name. Apply dengan select OK dan restart system.
![image](https://github.com/diotriandika/learn-networking/assets/109568349/8b6e8787-40f0-4fbc-b4c8-db9d5ff0b486)

Verifikasi menggunakan `Command Prompt` dengan mengeksekusi perintah `hostname`
![image](https://github.com/diotriandika/learn-networking/assets/109568349/e1b5bb1e-ff2c-4bc6-b757-8bc27bc5972b)

## Reset System ID Windows dengan Sysprep (Opsional)

