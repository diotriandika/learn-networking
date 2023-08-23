# Objective 
![image](https://github.com/diotriandika/learn-networking/assets/109568349/7bb088a3-74a0-4b3e-a179-dbbb4d8d7fa1)
## Penyelesaian Task 1
1. Instal nginx & firewalld and configuring to listen on port 8080
   - `sudo apt-get install nginx firewalld -y` & `systemctl enable nginx`
## Penyelesaian Task 2
   - buat direktori untuk domain, secara default biasanya ada di /var/www/ namun karena dalam task kita diinstruksikan untuk mengubah root document ke /opt/lab16 jadi kita buat index.html di /opt/lab16
   - `sudo mkdir -p /opt/lab16`
## Penyelesaian Task 3
   - edit index.html
   - `sudo nano /opt/lab16/index.html`
   - buat nginx server block di `/etc/nginx/sites-available/default dengan mengedit default seperti dibawah
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/95204d72-1236-404b-b370-4f12f0c48a78)
   - buat symbolic link kalau membuat serverblock baru. namun jika mengedit default, udh otomatis ada symbolic link
   - edit /etc/hosts sebelah localhost dengan domain yang sudah dibuat barusan.
## Penyelesaian Task 4 
   - Aktifkan ip forwarding `sudo sysctl net.ipv4.ip_forward=1` kemudian edit `/etc/sysctl` uncomment #net.ipv4.ip_forward=1
   - Konfigurasi Firewall
   - Set default firewall ke public dengan command `sudo firewall-cmd --set-default=public`
   - Aktifkan zone public dengan ngebind ke interface. `firewall-cmd --permanent --zone=public --change-interface=enp0s8`
   - Set agar zone public hanya menerima service HTTP melalui port yang sudah diset sebelumya `sudo firewall-cmd --permanent --zone=public --add-service=http`
   - lalu reload servie firewalld `sudo firewall-cmd --reload`
   - Configure public agar bisa diakses dengan port 8080 `sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp`
   - cek active zones dengan  `firewall-cmd --get-active-zones`
   - source : https://www.redhat.com/sysadmin/beginners-guide-firewalld?sc_cid=7013a000003SdA3AAK&gclid=Cj0KCQjwuZGnBhD1ARIsACxbAVhTHo9tXGbU8_RS5PXhRguVxr3F0Fhgb-gWZ1c_XYzzxkili5tbimMaAqVfEALw_wcB
## Verifikasi
  - coba gunakan service `curl` untuk test servernya
  - `curl lnearher.id` maka akan muncul error seperti dibawah
  - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/9b72090d-2e69-49c5-a205-2abfe9bcf9de)
  - namun jika diakses dengan port 8080
  - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a66de068-2ea4-40b2-a5d6-d43abcd9ba63)

![image](https://github.com/diotriandika/learn-networking/assets/109568349/9732e1aa-2063-4d64-af99-4f30fc617481)

# **_You Completed The Task!_**
