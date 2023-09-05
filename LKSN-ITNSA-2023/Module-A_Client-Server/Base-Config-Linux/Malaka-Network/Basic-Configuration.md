# Basic Configuration di Malaka Network
Table of Contents :
- [x] IPv4 Addressing
- [x] Setup Hostname
- [x] IP Forwarding
## IPv4 Addressing
### Node Buton
Masuk ke node _buton.lks.id_ set IPv4 Static
```bash
nano /etc/network/interfaces
```
Format :
```
# The primary network interface
auto ens33
iface ens33 inet static
    address 45.8.17.1/24
    dns-nameserver 45.8.17.22

auto ens36
iface ens36 inet static
    address 10.196.10.25/25
    gateway 10.196.10.1/25
    dns-nameserver 10.196.10.22
```
### Node Aur
Masuk ke node _aur.lks.id_ set IPv4 Static
```
nano /etc/network/interfaces
```
Format :
```
# The primary network interface
auto ens33
iface ens33 inet static
    address 45.8.17.22/24
    gateway 45.8.17.1/24
```
### Node Cilik
Masuk ke node _cilik.lks.id_ set IPv4 Static
```
nano /etc/network/interfaces
```
Format :
```
# The primary network interface
auto ens33
iface ens33 inet static
    address 45.8.17.21/24
    gateway 45.8.17.1/24
    dns-nameserver 45.8.17.22
```
## Setup Hostname
### Node Buton
Set hostname ke _buton.lks.id_
```
hostnamectl set-hostname buton.lks.id
```
edit file `/etc/hosts`
```
127.0.0.1   buton.lks.id
```
### Node Aur
Set hostname ke _aur.lks.id_
```
hostnamectl set-hostname buton.lks.id
```
edit file `/etc/hosts`
```
45.8.17.22   aur.lks.id
```
### Node Cilik
Set hostname ke _cilik.lks.id_
```
hostnamectl set-hostname buton.lks.id
```
edit file `/etc/hosts`
```
45.8.17.22   cilik.lks.id
```
## IP-Forwarding di Buton
Masuk ke node _buton.lks.id_ edit file `sysctl.conf`
```bash
nano -l /etc/sysctl.conf
```
Uncomment di line '28'
```
 28 net.ipv4.ip_forward=1
```
## Verifikasi
Jika sudah reboot server dan cek konfigurasi.
