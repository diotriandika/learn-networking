# Karimata Networking

**Tasks:**

---

- Configure DHCP Server in muna.lks.id
  - You can freely use any tools/package that provides standard DHCP.
  - Respond only to requests received in Karimata Network.
  - Use the IP range: 10.200.2.40-10.200.2.50
  - Give DNS Address to Karimata DNS
  - Give default gateway to muna.lks.id.
- Make sure muna.lks.id can route traffic from Karimata Network to two other networks.

---

### Step 1 - Configure DHCP Server 

Install `isc-dhcp-server` di muna.lks.id 

```bash
$ sudo apt-get install isc-dchp-server
```

Buka file konfigurasi, dan sesuaikan dengan Task

```bash
$ sudo nano -l /etc/dhcp/dhcpd.conf
---
21 authoritative

50 subnet 10.200.2.0 netmask 255.255.255.128 {
51 	range 10.200.2.40 10.200.2.50;
52	option domain-name 10.200.2.13;
53 #
54	option routers 10.200.2.2;
55	option broadcast-address 10.200.2.127;
56	default-lease-time 600;
57	max-lease-time 7200;
58 }
---
$ sudo nano /etc/default/isc-dhcp-server
---
17 INTERFACESv4="ens36"
---
```

Restart dhcp server

```bash
$ sudo systemctl restart isc-dhcp-server
```

### Step 2 - Configure Karimata Network Traffic

Install `iptables`

```bash
$ sudo apt-get install iptables
```

Buat nat agar Karimata network bisa menjangkau Lombok Network 

> Disini sepertinya ada kesalahan dalam pembuatan soal, karena semestinya Karimata Network tidak boleh sampai ke Malaka Network (Task terakhir)

```bash
$ sudo /sbin/iptables -t nat -A POSTROUTING -o ens36 -j MASQUERADE
```

Simpan konfigurasi dan buat script untuk presistent nat

```bash
$ sudo /sbin/iptables-save > /etc/iptables.up.rule
$ sudo nano /etc/network/if-pre-up.d/iptables
---
#!/bin/sh
/sbin/iptables-restore < /etc/iptables.up.rule
---
$ sudo chmod +x /etc/network/if-pre-up.d/iptables
```

Coba reboot system, apakah table nat masih ada atau tidak

