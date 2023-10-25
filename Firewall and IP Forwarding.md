 # Firewall and IP Forwarding

**Tasks:**

---

- Configure buton.lks.id with iptables LOG module to log these traffics:
  - Traffic from Malaka Network to Karimata Network
  - Incoming DNS Request
  - Incoming ICMP Request
- Enable IP Forwarding in the required servers to with the following conditions:
  - Lombok Network must be able to reach Karimata Network
  - Karimata Network must be able to reach Lombok Network
  - Malaka Network must not be able to reach Lombok Network without using VPN
  - Malaka Network must not be able to reach Karimata Networki without using VPN
  - Lombok Network must not be able to reach Malaka Network
  - Karimata Network must not be able to reach Malaka Network

---

### Step 1 - Traffic Logging

Install iptables

```bash
$ sudo apt install iptables
```

Buat rule untuk logging 

```bash
# Rule logging traffic from Malaka to Karimata
$ /sbin/iptables -A FORWARD -s 45.8.17.0/24 -d 10.200.2.0/25 -j LOG --log-prefix "Malaka to Karimata: "

# Rule logging traffic Incoming DNS Request
$ /sbin/iptables -A INPUT -p tcp --dport 53 -j LOG --log-prefix "Incoming DNS Request: "
$ /sbin/iptables -A INPUT -p udp --dport 53 -j LOG --log-prefix "Incoming DNS Request: "

# Rule logging traffic Incoming ICMP Request
$ /sbin/iptables -A INPUT -p icmp -j LOG --log-prefix "Incoming ICMP Request: "
```

Disini saya ingin memasukan logging dari semua rule ke sebuah file

```bash
$ sudo nano /etc/rsyslog.d/iptables.conf
---
:msg,contains, "Incoming DNS Request: " -/var/log/firewall.log
:msg,contains, "Incoming ICMP Request: " -/var/log/firewall.log
:msg,contains, "Malaka to Karimata: " -/var/log/firewall.log
& ~
```

Restart rsyslog

```bash
$ sudo systemctl restart rsyslog
```

Buat rule menjadi persistent

```bash
$ sudo apt-get install iptables-persistent
---
> yes
> yes
---
# Save Rule
$ /sbin/iptables-save > /etc/iptables/rules.v4 
```

### Step 2 - IP Forwarding Rule

- [x] Lombok Network must be able to reach Karimata Network 
- [x] Karimata Network must be able to reach Lombok Network

Secara konfigurasi diatas seharusnya bisa saling terhubung, dengan catatan pada muna.lks.id dan buton.lks.id sudah diaktifkan IP Forwarding. Namun sepertinya ada kesalahan dalam pembuatan soal, karena seharusnya pada buton.lks.id memiliki gateway agar bisa reach karimata network.

---

- [x] Malaka Network must not be able to reach Lombok Network without using VPN
- [x] Malaka Network must not be able to reach Karimata Networki without using VPN

Rule diatas jika sudah dikonfigurasi VPN harusnya juga dapat terkoneksi dengan kedua network

---

- [x] Lombok Network must not be able to reach Malaka Network
- [x] Karimata Network must not be able to reach Malaka Network

Untuk Task ini kita bisa melakukan blocking dengan rule firewall dibawah pada buton.lks.id

```bash
# Blocking Lombok network to reach Malaka Network
$ /sbin/iptables -A FORWARD -s 10.196.10.0/25 -d 45.8.17.0/24 -j DROP
# Blocking Karimata Network to reach Malaka Network
$ /sbin/iptables -A FORWARD -s 10.200.2.0/25 -d 45.8.17.0/24 -j DROP
```

