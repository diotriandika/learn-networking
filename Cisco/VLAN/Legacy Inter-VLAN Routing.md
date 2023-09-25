# Legacy Inter-VLAN Routing

Pada Legacy Inter-VLAN Routing, setiap interface pada router akan terhubung ke port switch di VLAN yang berbeda. Interface Router berfungsi sebagai gateway default untuk local host di subnet VLAN. Cara ini sangat terbatas dalam skalabilitas, karena setiap kita ingin menambahkan VLAN baru, perlu diingat bahwa port pada router itu sangatlah terbatas. Sebagai contoh sebuah router memiliki 4 port, jika semuanya ingin digunakan VLAN, maka hanya 4 segmen VLAN yang bisa dibuat.

Topologi:

![image-20230925223307729](https://github.com/diotriandika/learn-networking/assets/109568349/93695964-95c3-4765-943a-decca86c2147)

VLAN Table :
| VLAN ID | Name   | Interface | Network         |
| ------- | ------ | --------- | --------------- |
| 10      | VLAN10 | Fa0/3     | 192.168.10.0/24 |
| 20      | VLAN20 | Fa0/4     | 192.168.20.0/24 |

Addressing Table:

| Device | IP Address / Prefix | Interface  |
| ------ | ------------------- | ---------- |
| Router | 192.168.10.1/24     | Gig 0/0.10 |
|        | 192.168.20.1/24     | Gig 0/1.20 |
| PC0    | 192.168.10.2/24     | Fa0        |
| PC1    | 192.168.20.2/24     | Fa0        |

## Legacy Inter-VLAN Configuration Steps

### Step 1 (Addressing VLAN)

**Router>**

```markdown
Router> enable
Router# configure terminal

# Aktifkan Interface yang akan digunakan sebagai VLAN
Router(config)# interface gigabitEthernet 0/1
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface gigabitEthernet 0/1
Router(config-if)# no shutdown
Router(config-if)# exit

# Tambahkan IP pada Sub-Interface VLAN 10
Router(config)# interface gigabitEthernet 0/0.10
Router(config-if)# encapsulation dot1Q
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# description Default Gateway VLAN 10
Router(config-if)# exit

# Tambahkan IP pada Sub-Interface VLAN 20
Router(config)# interface gigabitEthernet 0/1.20
Router(config-if)# encapsulation dot1Q
Router(config-if)# ip address 192.168.20.1 255.255.255.0
Router(config-if)# description Default Gateway VLAN 20
Router(config-if)# exit
```

### Step 2 (Mengaktifkan Jalur VLAN Switch L2)

**Switch>**

```markdown
Switch> enable
Switch# configure terminal

# Buat VLAN Table sesuai dengan Appendix
Switch(config)# vlan 10
Switch(config-vlan)# name VLAN10
Switch(config-vlan)# vlan 20
Switch(config-vlan)# name VLAN20
Switch(config-vlan)# exit

# Definisikan port yang terhubung ke router sebagai Trunk Port yang membawa semua informasi VLAN
# VLAN 10 Trunk
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit

# VLAN 20 Trunk
Switch(config)# interface fastEthernet 0/2
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit

# Definisikan port yang akan digunakan untuk VLAN Access
# VLAN10
Switch(config)# interface fastEthernet 0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

# VLAN20
Switch(config)# interface fastEthernet 0/4
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit
```

### Verifikasi

```markdown
Switch# show vlan
```

Coba lakukan ping antar kedua host, jika reply artinya Legacy Inter-VLAN berhasil.
