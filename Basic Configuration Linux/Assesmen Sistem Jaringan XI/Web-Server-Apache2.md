# Langkah Langkah Konfigurasi
Apache merupakan sebuah service yang menyediakan layanan HTTP.
## Installing Apache
   Update local package kemudian install apache2

     sudo apt update && sudo apt install apache2 -y

   Atur firewall menggunakan utility `ufw` atau `firewalld`, disini kita menggunakan ufw dan dimulai dengan list available application profiles

     sudo ufw app list

   
