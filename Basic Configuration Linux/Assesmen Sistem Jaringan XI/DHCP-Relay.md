# Langkah - Langkah Instalasi dan Konfigurasi DHCP Server dengan DHCP Relay
DHCP-Relay pada Linux merupakan sebuah program managemen distribusi IP dari DHCP-Server. Pada Debian, DHCP-Relay merupakan sebuah metode untuk distribusi IP Address ke perangkat client dengan memanfaatkan DHCP server yang terpusat pada router lain. Sehingga bisa dikatakan router yang menjadi DHCP relay hanya meneruskan "DHCP Request" dari perangkat client ke DHCP server. Hal ini sangat membantu jika perangkat-perangkat client tidak berada dalam satu network dengan DHCP Server.

Contoh Topologi :
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/f28476fe-0bdf-4a09-9fb2-290840c1f51f)

## Prerequisite :
- Sudah terkonfigurasi IP Address di node server dan router

Konfigurasi IP Saya:

DHCP-Relay RTR (Node-1)
>![image](https://github.com/diotriandika/learn-networking/assets/109568349/eac1b5cc-86aa-4b3a-993e-314e20e941c0)

DHCP-Server SRV (Node-2)
>![image](https://github.com/diotriandika/learn-networking/assets/109568349/b219e38f-43a9-4a34-82f8-e7aca46a108c)

## Instalasi DHCP-Server
Install `isc-dhcp-server` di Node yang menyediakan DHCP-Server. Untuk cara Installasi dan Konfigurasi DHCP-Server cek [disini](https://github.com/diotriandika/lnearher-public-repository/blob/edda35884e18f67bd06b6982805cebac8eb612e5/ASJ-Linux/DHCP-Server.md), akan tetapi terdapat perbedaan pada konfigurasi file `dhcpd.conf`

Edit file `dhcpd.conf`
```
sudo nano /etc/dhcp/dhcpd.conf
```
Ikuti format dibawah
```
uncomment :
 21 authoritative;

 30 subnet <ip-node-2> netmask <subnetmask-ip-node-2> {
 31 }
 32
 33 subnet <netowrk-ip-dhcp> netmask <subnetmask-ip-dhcp> {
 34 }

 52 # A slightly different configuration for an internal subnet.
 53 subnet <network-ip-dhcp> netmask <subnetmask-ip-dhcp> {
 54   range <start-ip-dhcp> <end-ip-dhcp>;
 55   option domain-name-servers <dns-nameserver>;
 56 #  option domain-name "internal.example.org";
 57   option routers <ip-gateway-dhcp>;
 58   option broadcast-address <ip-broadcast-dhcp>;
 59   default-lease-time 600;
 60   max-lease-time 7200;
 61 }
```
Contoh :
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e15a50ca-9ab3-4a8f-84e3-3fa8bb5ba990)
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/640e8ca0-647b-4237-84fb-e623c7168cfd)

Exit dan save, kemudian restart service

## Installasi DHCP-Relay
Update package dengan `apt-get update`

Install `isc-dhcp-server`
```
sudo apt-get install isc-dhcp-server
```
Selanjutnya akan muncul menu seperti dibawah.
## Konfigurasi DHCP-Relay
>![image](https://github.com/diotriandika/learn-networking/assets/109568349/99985739-d06b-4bcb-8681-0c2df01718b0)
>
Dsini masukan IP dari Node DHCP-Server. _Ingat, IP Dari Node/Host penyedia DHCP-Server, bukan IP yang akan didistribusikan_

Pilih OK. 

Menu selanjutnya adalah memasukan informasi Interface mana yang akan dipakai untuk mendistribusikan DHCP-Server. Disini saya menggunakan `ens5` yang terhubung ke client
>![image](https://github.com/diotriandika/learn-networking/assets/109568349/f5dd2255-1429-452b-b7d7-424272c5501e)

Untuk menu selanjutnya bisa dikosongkan saja, dan pilih OK.

> Note : Jika salah dalam mengisi opsi - opsi diatas, kita bisa konfigurasi ulang dengan menjalankan command
> ```
> dpkg-reconfigure isc-dhcp-relay
> ```

Restart service `isc-dhcp-relay`
```
sudo systemctl restart isc-dhcp-relay
```
Terakhir kita aktifkan dengan menjalankan command 
```
sudo dhcrelays <ip-node-dhcp-server>
```
Contoh
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e4f36878-4aac-4a4e-b742-f280c99ff094)

## Verifikasi
Cek client apakah sudah mendapatkan IP dari DHCP apa belum
![image](https://github.com/diotriandika/learn-networking/assets/109568349/6e634e90-d776-4b0b-bbb7-b9df28b02e18)

**_Done_**
