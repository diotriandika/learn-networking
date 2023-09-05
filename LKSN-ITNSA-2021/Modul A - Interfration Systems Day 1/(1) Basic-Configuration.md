# Basic Configuration ( Network Infrastructure )
Disini saya sudah mengatur setiap Interface dari Networking Devices & End Device sesuai dengan Appendix
- [x] Configure IP Address all devices
- [x] Enable Login SSH with user **patah** according to appendix and user are able to enter configuration terminal in router
- [x] Allowing user/server admin to access SSH all Network Devices
- [x] Setting Up additional password in Router with **Skills39**
## Task 1
Disini saya akan mulai dengan mengatur IP Address dari RTB, RTX, dan Clinet (Budi-PC)
### RTX
1. Masuk ke RTX, **enable** dan masuk ke **configure terminal** lalu cek Interface dengan command
   ```
   do sh ip int br
   ```
   disini semua interface masih dalam keadaan mati dan tidak ada ip terkonfigurasi
2. Konfigurasi IP, saya akan mulai dengan mengkonfigurasi IP untuk jalur ke `edge.sabang.net` yang mana sesuai dengan appendix ini menggunakan sub-interface atau vlan.
   ```bash
   # masuk interface gig - 1.10 (*.10 disini menandakan vlan id dari sub-interface)
   interface gigabitEthernet 1.10
   
   # enable interface
   no shutdown

    # aktifkan trunk dan routing antar VLAN dengan IEEE 802.1Q
   encapsulation dot1Q
   
   # masukan ip address
   ip address 172.17.1.2 255.255.255.252

   # exit interface
   exit
   ```
   Sekarang saya konfigurasi IP untuk interface jalur ke `CA` / `indonesia.com`
   ```bash
   # masuk interface gig - 1.20 (*.10 disini menandakan vlan id dari sub-interface)
   interface gigabitEthernet 1.20
   
   # enable interface
   no shutdown

   # aktifkan trunk dan routing antar VLAN dengan IEEE 802.1Q
   encapsulation dot1Q
   
   # masukan ip address
   ip address 192.168.192.1 255.255.248.0

   # exit interface
   exit
   ```
   Lanjut mengkonfigurasi IP untuk jalur ke `Budi-PC`
   ```bash
   # masuk interface gig 2
   interface gigabitEthernet 2
   
   # enable interface
   no shutdown
   
   # masukan ip address
   ip address 172.10.1.1 255.255.0.0

   # exit interface
   exit
   ```
   dan konfigurasi IP penghubung ke Router `RTB`
   ```bash
   # masuk interface gig 3
   interface gigabitEthernet 3
   
   # enable interface
   no shutdown
   
   # masukan ip address
   ip address 30.9.65.9 255.255.255.248

   # exit interface
   exit
   ```
### RTB
1. Masuk ke RTB, **enable** dan masuk ke **configure terminal** lalu cek Interface dengan command
   ```
   do sh ip int br
   ```
   cek interface yang akan digunakan
2. Konfigurasi IP untuk penghubung ke Router `RTX` di Interface gigabitEthernet 1
   ```bash
   # masuk interface gig 1
   interface gigabitEthernet 1
   
   # enable interface
   no shutdown
   
   # masukan ip address
   ip address 30.9.65.11 255.255.255.248

   # exit interface
   exit
   ```
   dan Konfigurasi IP untuk penghubung ke `DC` dan `Storage` atau `merauke.net`
   ```bash
   # masuk interface gig 2
   interface gigabitEthernet 2
   
   # enable interface
   no shutdown
   
   # masukan ip address
   ip address 172.17.9.1 255.255.252.0

   # exit interface
   exit
   ```
### Budi-PC
Untuk mengkonfigurasi IP Address di Budi-PC, lakukan dengan masuk ke Control Panel > Network & Internet > Network and Sharing Center > Change Adapter Settings > Select Interface yang terhubung ke RTB > Right-Click dan Select Properties > lalu Select Internet Protocol version 4 atau IPv4  

Set IP sesuai dengan Appendix
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/daa48103-856f-41be-a828-61cc2cb31db4)
