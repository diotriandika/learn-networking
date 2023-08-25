# Langkah - langkah Mengaktifkan IP Forwarding
IP Forwarding berfungsi agar dua network atau lebih bisa saling bertukar informasi ataupun data.
## Mengaktifkan IP Forwarding via sysctl.conf
Untuk mengaktifkan IP Forwarding via `sysctl.conf` bisa melakukan hal berikut

Edit file `sysctl.conf`

    sudo nano /etc/sysctl.conf

lalu uncomment pada line `net.ipv4_forwarding =1`
## Enable with Root Privilege
Jika dalam mode root/user root bisa gunakan command dibawah

    echo '1' > /proc/sys/net/ipv4/ip_forward
