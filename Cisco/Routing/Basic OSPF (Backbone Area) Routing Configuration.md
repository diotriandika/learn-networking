# Basic OSPF Routing Configuration

OSPF atau Open Shortest Path First merupakan sebuah protokol routting secara otomatis atau dinamis yang mampu menjaga, mengatur, dan mendistribusikan informasi routing atau Routing Table antar network setiap terjadinya perubahan jaringan secara dinamis. 

> Quick Tip: Jika kita sebelumnya menambahkan Network yang dituju secara manual satu persatu, pada OSPF kita hanya perlu memasukan Network yang kita punya saja. Secara otomatis Router yang terhubung di Area yang sama akan saling bertukar informasi terkait Routing Table mereka.

OSPF dibedakan menjadi 2 berdasarkan areanya, yakni Single Area (hanya menggunakan Area 0 atau Area Backbone) dan Multiarea (menggunakan area backbone dan area lain). Jika menggunakan lebih dari 1 area atau Multiarea, area lain **harus** terhubung dengan backbone area.

Apa itu Backbone area?

> Backbone area adalah pusat dari OSPF, dimana semua area akan terkoneksi langsung pada area ini. Area ini akan selalu diberi label area 0. Pertukaran informasi routing network terjadi pada area ini

Referensi (lengkap): 

- https://medium.com/@Irfan.Luthfi/what-is-ospf-63078be281f8#:~:text=Backbone%20Area%20adalah%20pusat%20dari,network%20terjadi%20pada%20area%20ini
- https://www.ciscopress.com/articles/article.asp?p=2294214

**Topologi:**

![image-20230927092233996](https://github.com/diotriandika/learn-networking/assets/109568349/4ad4777d-1a11-48cb-97aa-48e1d4ae2ca4)

Addressing Table:

| Device  | IP Address / Prefix | Interface | Area     |
| ------- | ------------------- | --------- | -------- |
| WST-1   | 10.10.10.1/30       | Gig 0/0   | Backbone |
|         | 192.168.10.1/24     | Gig 0/1   | Backbone |
| ISP     | 10.10.10.2/30       | Gig 0/0   | Backbone |
|         | 20.20.20.1/30       | Gig 0/1   | Backbone |
|         | 172.16.65.1/24      | Gig 0/2   | Backbone |
| EST-1   | 20.20.20.2/30       | Gig 0/0   | Backbone |
|         | 192.168.20.1/24     | Gig 0/1   | Backbone |
| WS-CLI  | 192.168.10.2/24     | Fa0       |          |
| ISP-SRV | 172.16.65.2/24      | Fa0       |          |
| EST-CLI | 192.168.20.2/24     | Fa0       |          |

## Basic OSPF (Backbone Area) Configuration Steps

### Step 1 (Router Addressing)

**WST-1>**

```markdown
WST-1> enable
WST-1# configure terminal

# Tambahkan IP Address (Sebagai Link ke ISP)
WST-1(config)# interface gigabitEthernet 0/0
WST-1(config-if)# ip address 10.10.10.1 255.255.255.252
WST-1(config-if)# description Link to ISP (opsional)
WST-1(config-if)# no shutdown
WST-1(config-if)# exit

# Tambahkan IP Address (ke WS-CLT)
WST-1(config)# interface gigabitEthernet 0/1
WST-1(config-if)# ip address 192.168.10.1 255.255.255.0
WST-1(config-if)# description Default Gateway WST-CLT (opsional)
WST-1(config-if)# no shutdown
WST-1(config-if)# exit
```

**ISP>**

```markdown
ISP> enable
ISP# configure terminal

# Tambahkan IP Address (Sebagai Link ke WST-1)
ISP(config)# interface gigabitEthernet 0/0
ISP(config-if)# ip address 10.10.10.2 255.255.255.252
ISP(config-if)# description Link to WST-1
ISP(config-if)# no shutdown
ISP(config-if)# exit

# Tambahkan IP Address (Sebagai Link ke EST-1)
ISP(config)# interface gigabitEthernet 0/1
ISP(config-if)# ip address 20.20.20.1 255.255.255.252
ISP(config-if)# description Link to EST-1
ISP(config-if)# no shutdown
ISP(config-if)# exit

# Tambahkan IP Address (ke ISP-SRV)
ISP(config)# interface gigabitEthernet 0/2
ISP(config-if)# ip address 172.16.65.1 255.255.255.0
ISP(config-if)# description Default Gateway ISP-SRV
ISP(config-if)# no shutdown
ISP(config-if)# exit
```

**EST-1>**

```markdown
EST-1> enable
EST-1# configure terminal

# Tambahkan IP Address (Sebagai Link ke ISP)
EST-1(config)# interface gigabitEthernet 0/0
EST-1(config-if)# ip address 20.20.20.2 255.255.255.252
EST-1(config-if)# description Link to ISP
EST-1(config-if)# no shutdown
EST-1(config-if)# exit

# Tambahkan IP Address (ke EST-CLT)
EST-1(config)# interface gigabitEthernet 0/1
EST-1(config-if)# ip address 192.168.20.1 255.255.255.0
EST-1(config-if)# description Default Gateway EST-CLT
EST-1(config-if)# no shutdown
EST-1(config-if)# exit
```

#### Verfikasi

Cek ulang konfigurasi dengan perintah

```markdown
<hostname># show running-config
```

Konfigurasi IP pada End Device secara Statis atau Dinamis

### Step 2 (OSPF Routing)

**WST-1>**

```markdown
# Aktifkan OSPF dengan Process ID bebas
WST-1(config)# router ospf 10

# Tambahkan semua Network dari WST-1
WST-1(config-router)# network 10.10.10.0 0.0.0.3 area 0
WST-1(config-router)# network 192.168.10.0 0.0.0.255 area 0

# Set Passive Interface (Interface yang tidak terhubung ke router, router tidak mengirimkan paket Hello ke Interface tersebut)
WST-1(config-router)# passive-interface GigabitEthernet 0/1
WST-1(config-router)# exit
```

**ISP>**

```markdown
# Aktifkan OSPF dengan Process ID bebas
ISP(config)# router ospf 10

# Tambahkan semua Network yang dari ISP
ISP(config-router)# network 10.10.10.0 0.0.0.3 area 0
ISP(config-router)# network 20.20.20.0 0.0.0.3 area 0
config-router)# network 172.16.65.0 0.0.0.255 area 0

# Set Passive Interface (Interface yang tidak terhubung ke router, router tidak mengirimkan paket Hello ke Interface tersebut)
ISP(config-router)# passive-interface gigabitEthernet 0/2
ISP(config-router)# exit
```

**EST-1>**

```markdown
# Aktifkan OSPF dengan Process ID bebas
EST-1(config)# router ospf 10

# Tambahkan semua Network yang dari EST-1
EST-1(config-router)# network 20.20.20.0 0.0.0.3 area 0
EST-1(config-router)# network 192.168.20.0 0.0.0.255 area 0

# Set Passive Interface (Interface yang tidak terhubung ke router, router tidak mengirimkan paket Hello ke Interface tersebut)
EST-1(config-router)# passive-interface gigabitEthernet 0/1
EST-1(config-router)# exit
```

#### Verifikasi

```markdown
# Melihat Neighbor Table
Router# show ip ospf neighbor

# Melihat Database Table
Router# show ip ospf database

# Melihat Routing Table 
Router# show ip route

# Spesifik OSPF Routing Table
Router# show ip route ospf
```

Coba lakukan ping antar end device
