# Langkah - Langkah Instalasi OpenSSH sebagai Remote Access Server
OpenSSH merupakan sebuah alat yang biasanya digunakan untuk akses remote sebuah server, file, dan lain lain.
## Instalasi OpenSSH
Update list package dengan `sudo apt update` 

Install OpenSSH 

    sudo apt install openssh-server -y

Sebenarnya sekarang kita sudah bisa mekakses server melalui remote/ssh, tapi jika perlu bisa melakukan konfigurasi keamanan seperti dibawah.
## Konfigurasi OpenSSH
Edit file `sshd_config`

    sudo nano /etc/ssh/sshd_conf

Untuk mengatur jalur port yang didukung ketika melakukan remote access, uncomment port dan sesuaikan port dengan kebutuhan

![image](https://github.com/diotriandika/learn-networking/assets/109568349/c80adfd9-915f-462c-984a-54d9533c5282)

Kemudian jika ingin agar bisa mengakses user root, uncomment pada line berikut dan rubah `prohibit-password` menjadi `yes`

![image](https://github.com/diotriandika/learn-networking/assets/109568349/d7e5e780-ac41-4902-873a-a0955af7988c)

## Verifikasi
Coba akses melalui port default untuk verifikasi apakah accessible melalui port 22, dan selanjutnya coba melalui port yang sudah diset.

![image](https://github.com/diotriandika/learn-networking/assets/109568349/ddfac790-b096-4e3f-a6c7-43c233f22d15)

> Main Repo : https://github.com/diotriandika/learn-networking

