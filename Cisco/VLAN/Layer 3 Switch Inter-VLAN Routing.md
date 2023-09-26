# Layer 3 Switch Inter-VLAN Routing

Menggunakan L3 Switch atau Multilayer Switch merupakan cara yang paling efektif dan efisien untuk  melakukan Inter-VLAN Routing. Metode ini menggunakan Switching langsung pada SVI (Switch Virtual Interface).

Topologi:

![image-20230926090233746](https://github.com/diotriandika/learn-networking/assets/109568349/c5ea943f-2967-4f00-88b9-868394aca3a8)

VLAN Table

| VLAN ID | Name   | Interface | Network         |
| ------- | ------ | --------- | --------------- |
| 10      | VLAN10 | Fa 0/1    | 192.168.10.0/24 |
| 20      | VLAN20 | Fa 0/2    | 192.168.10.0/24 |

Addressing Table:

| Device | IP Address / Prefix | Interface |
| ------ | ------------------- | --------- |
| L3SW   | 192.168.10.1/24     | vlan 10   |
|        | 192.168.20.1/24     | vlan 20   |
| PC0    | 192.168.10.2/24     | Fa0       |
| PC1    | 192.168.20.2/24     | Fa0       |

## Layer 3 Switch Inter-VLAN Routing Configuration Steps

### Step 1 (VLAN Addressing)

**L3SW>**

```markdown
L3SW> enable
L3SW# configure terminal

# Buat VLAN
L3SW(config)# vlan 10
L3SW(config-vlan)# name VLAN10
L3SW(config-vlan)# vlan 20
L3SW(config-vlan)# name VLAN20
L3SW(config-vlan)# exit

# Tambahkan IP Address pada masing - masing SVI
# VLAN 10
L3SW(config)# interface vlan 10
L3SW(config-if)# ip address 192.168.10.1 255.255.255.0
L3SW(config-if)# description Default Gateway VLAN 10 SVI
L3SW(config-if)# no shutdown
L3SW(config-if)# exit

# VLAN 20
L3SW(config)# interface vlan 20
L3SW(config-if)# ip address 192.168.20.1 255.255.255.0
L3SW(config-if)# description Default Gateway VLAN 20 SVI
L3SW(config-if)# no shutdown
L3SW(config-if)# exit
```

### Step 2 (Access Port VLAN)

**L3SW>**

```markdown
# Definisikan port yang akan digunakan untuk VLAN Access
# VLAN 10
L3SW(config)# interface fastEthernet 0/1
L3SW(config-if)# switchport mode access
L3SW(config-if)# switchport access vlan 10
L3SW(config-if)# exit

# VLAN 20
L3SW(config)# interface fastEthernet 0/2
L3SW(config-if)# switchport mode access
L3SW(config-if)# switchport access vlan 20
L3SW(config-if)# exit
```

#### Verifikasi

Coba untuk ping dari PC0 ke PC1
Hasilnya akan gagal, karena tidak seperti Router, L3 Switch harus diaktifkan untuk global ip routingnya.

### Step 3 (IP Routing)

**L3SW**

```markdown
# Aktifkan Global IP Routing
L3SW(config)# ip routing
```

#### Verifikasi 

Sekarang coba lagi untuk ping dari PC0 ke PC1 dan sebaliknya

```markdown
# Cek Addressing SVI
L3SW# show ip interface brief
```

