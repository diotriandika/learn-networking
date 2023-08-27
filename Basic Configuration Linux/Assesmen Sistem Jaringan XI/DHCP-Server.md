# Langkah - Langkah Instalasi dan Konfigurasi DHCP-Server
DHCP Server merupakan sebuah program yang pada server yang memungkinkan untuk pengalamatan IP pada Client secara dinamis atau Otomatis.
## Installasi & Konfigurasi DHCP-Server
Install program `isc-dhcp-server`
```
sudo apt-get install isc-dhcp-server -y
```
Selanjutnya aktifkan Interface yang akan digunakan sebagai DHCP-Server
```
sudo nano -l /etc/default/isc-dhcp-server
```
dan tambahakan interface pada line ke 17 seperti dibawah
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/71da5434-1d62-4573-9f59-febb53e8d013)

Exit dengan CTRL+X kemudian Y dan ENTER.

Selanjutnya Edit file `dhcpd.conf`
```
sudo nano -l /etc/dhcp/dhcpd.conf
```
Uncomment pada line berikut
```
21 authoritative;

30 subnet <network-ipv4> netmask <subnetmask> {
31 }

49 # A slightly different configuration for an internal subnet.
50 subnet <network-ip> netmask <subnetmask> {
51   range <start-ip> <end-ip>;
52   option domain-name-servers <ip-dns/domain>;
53 #  option domain-name "internal.example.org";
54   option routers <ip-gateway>;
55   option broadcast-address <ip-broadcast>;
56   default-lease-time 600;
57   max-lease-time 7200;
58 }
```
> Contoh :
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/3e847ba4-bb89-4f00-ba8f-babeb480b191)
>

Save dan Exit dengan CTRL+X kemudian Y dan ENTER

Restart service `isc-dhcp-server`
```
sudo systemctl restart isc-dhcp-server`
```
Done
