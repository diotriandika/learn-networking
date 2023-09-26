# Layer 3 Switch Inter-VLAN Routing (with Router)

Agar VLAN dapat diakses oleh Device Layer 3 lain, VLAN tersebut harus menggunakan static atau dynamic routing agar keduanya bisa saling terhubung. Untuk mengaktifkan routing pada layer 3 Switch, sebuah port yang sudah dirouting harus terkonfigurasi. 

Topologi:

![image-20230926100711077](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230926100711077.png)

VLAN Table:

| VLAN ID | Name   | Network         | Interface |
| ------- | ------ | --------------- | --------- |
| 10      | VLAN10 | 192.168.10.0/24 | Fa 0/1    |
| 20      | VLAN20 | 192.168.20.0/24 | Fa 0/2    |

Addressing Table:

| Device | IP Address / Prefix | Interface |
| ------ | ------------------- | --------- |
| Router | 172.16.65.1/24      | Gig 0/0   |
|        | 10.10.10.1/24       | Gig 0/1   |
| L3SW   | 10.10.10.2/24       | Gig 0/1   |
|        | 192.168.10.1/24     | vlan 10   |
|        | 192.168.20.1/24     | vlan 20   |
| SRV1   | 172.16.65.2/24      | Fa0       |
| PC0    | 192.168.10.2/24     | Fa0       |
| PC1    | 192.168.20.2/24     | Fa0       |

## Layer 3 Switch Inter-VLAN Routing (with Router) Configuration Steps

### Step 1 (Router Addressing)

**Router>**

```markdown
Router> enable
Router# configure terminal

# Konfigurasi IP Address (to SRV1)
Router(config)# interface gigabit
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 172.16.65.1 255.255.255.0
Router(config-if)# description Default Gateway SRV1
Router(config-if)# no shutdown

# Konfigurasi IP Address (to L3SW)
Router(config)# interface gigabitEthernet 0/1
Router(config-if)# ip address 10.10.10.1 255.255.255.0
Router(config-if)# description Default Gateway L3SW
Router(config-if)# no shutdown
```

#### Verifikasi

Cek kembali IP Address apa sudah sesuai
```markdown
Router# show ip interface brief
Router# show running-config | sec Gig
```

### Step 2 (L3 Switch Addressing)

**L3SW>**

```markdown
L3SW> enable
L3SW# configure terminal

# Konfigurasi IP Address (to Router)
L3SW(config)# interface gigabitEthernet 0/1
L3SW(config-if)# no switchport
L3SW(config-if)# ip address 10.10.10.2 255.255.255.0
L3SW(config-if)# no shutdown 
L3SW(config-if)# description Linked Router
L3SW(config-if)# exit

# Buat VLAN
L3SW(config)# vlan 10
L3SW(config-vlan)# name VLAN10
L3SW(config-vlan)# vlan 20
L3SW(config-vlan)# name VLAN20
L3SW(config-vlan)# exit
L3SW(config)# 0

# Addressing VLAN pada SVI
# VLAN 10
L3SW(config)# interface vlan 10
L3SW(config-if)# ip address 192.168.10.1 255.255.255.0
L3SW(config-if)# description Default Gateway VLAN10
L3SW(config-if)# exit

# VLAN 20
L3SW(config)# interface vlan 20
L3SW(config-if)# ip address 192.168.20.1 255.255.255.0
L3SW(config-if)# description Default Gateway VLAN20
L3SW(config-if)# exit
```

#### Verifikasi

```markdown
L3SW# show running-config | sec Vlan
L3SW# show ip interface brief
L3SW# show vlan
```

Coba untuk ping dari PC1 ke PC0 dan sebaliknya

Kedua VLAN tersebut masih belum terhubung, maka dari itu kita aktifkan global ip routing pada L3SW

### Step 3 (Global IP Routing)

**L3SW>**

```markdown
# Aktifkan global ip routing
L3SW(config)# ip routing
```

#### Verifikasi

Coba ping kembali dari PC0 ke PC1 dan sebaliknya

Sekarang seharusnya PC0 dan PC1 bisa saling berkomunikasi, akan tetapi keduanya tidak bisa terhubung ke network yang ada pada Layer 3 lain yaitu Network **172.16.65.0/24**

Maka dari itu kita bisa melakukan tahap Routing pada Router dengan Static Routing atau Dynamic Routing. Disini sebagai contoh kita akan menggunakan Dynamic Routing **OSPF (Open Shortest Path First)**

### Step 4 (Dynamic Routing - OSPF)

**Router>**

```markdown
Router> enable
Router# configure terminal

# Buat Routing Table OSPF
Router(config)# router ospf 5

# Tambahkan Network pada Router yang ingin diberikan akses routing
Router(config-router)# network 172.16.65.0 0.0.0.255 area 0
Router(config-router)# network 10.10.10.0 0.0.0.255 area 0
Router(config-router)# exit
```

**L3SW>**

```markdown
L3SW> enable
L3SW# configure terminal

# Buat Routing Table OSPF dengan Process ID yang sama
L3SW(config)# router ospf 5

# Tambahkan Network pada Switch yang ingin diberikan akses routing
L3SW(config-router)# network 10.10.10.0 0.0.0.255 area 0
L3SW(config-router)# network 192.168.10.0 0.0.0.255 area 0
L3SW(config-router)# network 192.168.20.0 0.0.0.255 area 0
L3SW(config-router)# exit
```

#### Verifikasi

```markdown
<hostname># show ip route
```

Coba untuk melakukan ping dari PC0/PC1 ke Router dan SRV1

