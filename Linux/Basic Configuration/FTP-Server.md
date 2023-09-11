# Langkah - langkah instalasi
## Install VSFTPD 
   Update repository kemudian instal vstpd
   
     sudo apt update
     sudo apt install vsftpd -y
## Allow FTP di firewall <ufw/firewalld>
   Allow port 20 & 21

     sudo ufw allow 20/tcp
     sudo ufw allow 21/tcp
## Konfigurasi Akses FTP
   Buat backup `vsftpd.conf`

     sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.orig

   Edit vsftpd configuration file

     sudo nano /etc/vsftpd.conf

   Edit sesuaikan seperti dibawah 
    
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/bce17e1c-d774-403d-8a20-548e42b7546d)
## Tambahkan FTP User
   Tambahkan user dengan `adduser` dan password <optional> `passwd`
   
     sudo adduser <user>

   Tambahkan user ke `vsftpd.userlist`

      sudo echo '<user>' | sudo tee /etc/vsftpd.userlist

## Restart service
   Restart VSFTPD service dan enable ketika booting system

     sudo systemctl restart vsftpd
     sudo systemctl enable vsftpd

_Detailed Websites : https://www.digitalocean.com/community/tutorials/how-to-set-up-vsftpd-for-a-user-s-directory-on-debian-10_ 
