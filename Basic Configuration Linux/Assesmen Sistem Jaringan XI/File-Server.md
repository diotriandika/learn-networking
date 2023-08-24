# Langkah - Langkah Installasi & Konfigurasi Samba
Samba merupakan sebuah tool yang mengimplementasikan protokol SMB (server message block). Samba diadaptasi oleh Windows dan Unix, sehingga menjadi tool yang berguna jika menggunakan kedua operasi sistem tersebut.
## Setup Samba Standalone
   Update repo dan install samba

    sudo apt update
    sudo apt install samba smbclient clifs-utils

   Buat Shared Samba Directory, disini kita buat /public dan /private sebagai shared folder public dan private directory.

    sudo mkdir /public
    sudo mkdir /private

## Samba Configuration
   Edit `Global` Samba configuration di `/etc/samba/smb.conf` dan uncomment

   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/6204bca0-10e2-460c-9dba-82c5e9fa428a)

   Diatas kita sudah membuat 2 direktori. Kita akan set `Public` share agar bisa diakses secara publik dan `Private` share akan meminta auth agar bisa diakses.
   
   Public Share Configuration
   
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d55e4928-4e2e-4731-8e8e-700420674fed)

   Private Share Configuration

   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/5d70ce16-d317-4114-aad1-3b78dbde361f)

## Samba User & Group
   Di Private Share diatas hanya akan memberikan akses kepada user dalam group `smbshare`, selanjutnya buat group `smbshare ` dan set permission direktori

    sudo group add smbshare
    sudo chrgp -R smbshare /private/
    sudo chrgp -R smbhsare /public/

   Set permission direktori

    sudo chmod 2270 /private/
    sudo chmod 2775 /public

   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/f63040e5-ce77-4ae6-a4da-defbb4c876ae)

   Buat user lokal Samba yang akan diberikan akses ke `Private` share. Jika ingin user tidak memiliki shell tambahkan argument seperti dibawah.

    sudo useradd -M -s /sbin/nologin <user> 

   Tambahkan user ke group `smbshare`

    sudo usermod -aG smbshare <user>

   Buat Password Samba untuk user kemudian Enable user

    sudo smbpasswd -a <user>
    sudo smbpasswd -e <user>

## Verifikasi Konfigurasi Samba
   test `/etc/samba/smb.conf` dengan `testparm` 

    testparm

   Restart samba service

     sudo systemctl restart nmbd

  Buat file untuk test di shared folder

    sudo mkdir /private/testfolder-priv /public/testfolder-pub
    touch /private/testfolder-priv /public/testfolder-pub

  Berikan Remote Akses ke Samba dengan cara menggunakan ufw allow. Jika ingin memberikan akses Samba dari specific network/IP tambahakan argument dibawah

    sudo ufw allow from <your-ip-or-network/your-subnet> any app Samba

  _**Detailed Tutorial at : https://kifarunix.com/quick-way-to-setup-samba-file-server-on-debian-10/**_
