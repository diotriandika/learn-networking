# Company VPN (Site-to-Site VPN)

**Tasks:**

---

- Configure Site-to-site vpn from buton.lks.id to aur.lks.id
  - Use openvpn
  - Make sure aur.lks.id have access to both Karimata Network and Lombok Network after VPN
  - Use IP range 10.250.1.0/24 for site-to-site connectivity.
  - Keep the VPN connection running

---

### Step 1 - Instalation & Configuration

Install openvpn dikedua server

```bash
$ sudo apt-get install openvpn
```

#### Do on muna.lks.id

Pindah ke direktori 

```bash
$ cd /etc/openvpn
```

Generate Key untuk auth

```bash
$ sudo openvpn --genkey secret vpn.key
```

Buat file konfigurasi

```bash
$ sudo nano s2s.conf
---
dev tun0
remote 45.8.17.23
ifconfig 10.250.1.1 10.250.1.2
secret /etc/openvpn/vpn.key
port 1195
user nobody
group nogroup
persist-tun
persist-key
verb 3
log /var/log/s2s.log
---
```

Copy `s2s.conf` dan `vpn.key` ke **aur.lks.id**

```bash
$ scp s2s.conf user@45.8.17.23:/home/user
$ scp vpn.key user@45.8.17.23:/home/user
```

#### Do in aur.lks.id

Pindah ke direktori

```bash
$ cd /etc/openvpn
```

Copy file konfigurasi yang sudah ditransfer

```bash
$ mv /home/user* /etc/openvpn
```

Edit file konfigurasi

```bash
$ sudo nano s2s.conf
---
dev tun0
remote 45.8.17.115
ifconfig 10.250.1.2 10.250.1.1
secret /etc/openvpn/vpn.key
port 1195
user nobody
group nogroup
persist-tun
persist-key
verb 3
route 10.196.10.0 255.255.255.128
route 10.200.2.0 255.255.255.128
log /var/log/s2s.log
---
```

#### Do on both server

Aktifkan ip forwarding di kedua server

```bash
$ sudo nano /etc/sysctl.conf
---
# uncomment
net_ipv4.ip_forward=1
---
```

Reboot system

Coba untuk saling ping, dan coba ping dari aur.lks.id ke network lombok

Coba juga untuk ping dari aur.lk.sid ke network karimata

#### Troubleshoot

Jika tidak bisa ping dari aur.lks.id ke karimata network, bisa tambahakn nat table di buton.lks.id

```bash
$ sudo apt-get install iptables
$ sudo /sbin/iptables -t nat -A POSTROUTING -o ens36 -j MASQUERADE
```

buat NAT tablenya presistent

coba ping lagi
