# Check Interfaces
    - ip a
# Edit Interfaces
    - sudo nano /etc/network/interfaces
    Add line selanjutnya
    - auto 'ens3/enp0s8/interface yang terhubung ke dhcp-server/nat'
      iface 'ens3' inet dhcp
    enter line
    - auto 'ens4/enp0s9/interface yang mau digunakan untuk terhubung ke host/client'
      iface 'ens4' inet static
                    address 192.168.1.1/24
    save config dengan kombinasi CTRL+X kemudian Y lalu ENTER
# 3estart Networking Service
    - sudo systemctl restart networking.services
    atau
    - /etc/init.d/networking restart
