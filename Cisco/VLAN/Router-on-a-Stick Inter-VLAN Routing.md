# Router-on-a-Stick Inter-VLAN Routing

Router-on-a-Stick Inter-VLAN Routing mengatasi keterbatasan dari Legacy Inter-VLAN Routing , karena pada metode ini hanya membutuhkan satu interface Ethernet fisik untuk merutekan lalu lintas antar VLAN pada jaringan. Interface Ethernet pada router Cisco dikonfigurasi sebagai Trunk dengan standar IEEE 802.1Q dan terhubung dengan trunk port di L2 Switch. Spesifiknya Router dikonifgurasi dengan subinterface untuk mengidentifikasi Routeable VLAN.

Topologi : 

![image-20230926081715721](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230926081715721.png)

VLAN Table: 

| VLAN ID | Name   | Network         |
| ------- | ------ | --------------- |
| 10      | VLAN10 | 192.168.10.0/24 |
| 20      | VLAN20 | 192.168.10.0/24 |

Addressing Table:

| Device | IP Address / Prefix | Interface  |
| ------ | ------------------- | ---------- |
| Router | 192.168.10.1/24     | Gig 0/0.10 |
|        | 192.168.20.1/24     | Gig 0/0.20 |
| PC0    | 192.168.10.2/24     | Fa0        |
| PC1    | 192.168.10.3/24     | Fa0        |
| PC2    | 192.168.20.2/24     | Fa0        |
| PC3    | 192.168.20.3/24     | Fa0        |

## Router on a Stick Inter-VLAN Routing Configuration Steps

### Step 1 (Addressing VLAN)

**Router>**

```markdown
Router> enable
Router# configure terminal

# Aktifkan Interface yang akan digunakan sebagai VLAN
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# no shutdown
Router(config-if)# exit

# Tambahkan IP pada Sub-Interface VLAN 10
Router(config)# interface gigabitEthernet 0/0.10
Router(config-subif)# encapsulation dot1Q
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# description Default Gateway VLAN 10 (Optional)
Router(config-subif)# exit

# Tambahkan IP pada Sub-Interface VLAN 20
Router(config)# interface gigabitEthernet 0/0.20
Router(config-subif)# encapsulation dot1Q
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# description Default Gateway VLAN 20 (Optional)
Router(config-subif)# exit
```

#### Verifikasi

```markdown
Router# show running-config | sec Gig
Router# show ip interface brief
```

### Step 2 (Membuat Jalur Trunking)

**VLANSW>**

```markdown
VLANSW> enable
VLANSW# configure terminal

# Definisikan port untuk trunk (To Router)
VLANSW(config)#interface fastEthernet 0/22
VLANSW(config-if)#switchport mode trunk
VLANSW(config-if)#exit
VLANSW(config)#

# Definisikan port untuk trunk (To VLANSW1)
VLANSW(config)#interface fastEthernet 0/23
VLANSW(config-if)#switchport mode trunk
VLANSW(config-if)#exit
VLANSW(config)#

# Definisikan port untuk trunk (To VLANSW2)
VLANSW(config)#interface fastEthernet 0/24
VLANSW(config-if)#switchport mode trunk
VLANSW(config-if)#exit
VLANSW(config)#

# Buat VLAN Table
VLANSW(config)#vlan 10
VLANSW(config-vlan)#name VLAN10
VLANSW(config-vlan)#vlan 20
VLANSW(config-vlan)#name VLAN20
VLANSW(config-vlan)#exit
VLANSW(config)#
```

### Step 3 (Mengaktifkan Jalur VLAN Switch L2)

**VLANSW1>**

```markdown
VLANSW1> enable
VLANSW1# configure terminal

# Buat VLAN Table
VLANSW1(config)#vlan 10
VLANSW1(config-vlan)#name VLAN10
VLANSW1(config-vlan)#vlan 20
VLANSW1(config-vlan)#name VLAN20
VLANSW1(config-vlan)#exit

# Definisikan port untuk trunk (To VLANSW)
VLANSW1(config)#interface fastEthernet 0/24
VLANSW1(config-if)#switchport mode trunk
VLANSW1(config-if)#exit

# Definisikan port yang akan digunakan untuk VLAN Access
# VLAN 10
VLANSW1(config)#interface fastEthernet 0/10
VLANSW1(config-if)#switchport mode access
VLANSW1(config-if)#switchport access vlan 10
VLANSW1(config-if)#exit

# VLAN 20
VLANSW1(config)#interface fastEthernet 0/20
VLANSW1(config-if)#switchport mode access
VLANSW1(config-if)#switchport access vlan 20
VLANSW1(config-if)#exit
```

**VLANSW2>**

```markdown
VLANSW2> enable
VLANSW2# configure terminal

# Buat VLAN Table
VLANSW2(config)#vlan 10
VLANSW2(config-vlan)#name VLAN10
VLANSW2(config-vlan)#vlan 20
VLANSW2(config-vlan)#name VLAN20
VLANSW2(config-vlan)#exit

# Definisikan port untuk trunk (To VLANSW)
VLANSW2(config)#interface fastEthernet 0/24
VLANSW2(config-if)#switchport mode trunk
VLANSW2(config-if)#exit

# Definisikan port yang akan digunakan untuk VLAN Access
# VLAN 10
VLANSW2(config)#interface fastEthernet 0/10
VLANSW2(config-if)#switchport mode access
VLANSW2(config-if)#switchport access vlan 10
VLANSW2(config-if)#exit

# VLAN 20
VLANSW2(config)#interface fastEthernet 0/20
VLANSW2(config-if)#switchport mode access
VLANSW2(config-if)#switchport access vlan 20
VLANSW2(config-if)#exit
```

#### Verifikasi

```markdown
<hostname-sw># show vlan brief
```

Coba untuk cross ping antar VLAN