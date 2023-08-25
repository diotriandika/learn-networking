# Langkah - langkah Mengaktifkan IP Forwarding
IP Forwarding berfungsi agar dua network atau lebih bisa saling bertukar informasi ataupun data.
## Mengaktifkan IP Forwarding via sysctl.conf
Untuk mengaktifkan IP Forwarding via `sysctl.conf` bisa melakukan hal berikut

Edit file `sysctl.conf`

    sudo nano /etc/sysctl.conf

lalu uncomment pada line `net.ipv4_forwarding =1`

Save file 

    sudo sysctl -p /etc/sysctl.conf

Selanjutnya restart service dengan

    sudo /etc/init.d/procps restart

done

