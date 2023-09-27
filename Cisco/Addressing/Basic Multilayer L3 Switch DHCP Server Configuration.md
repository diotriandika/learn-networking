# Basic Multilayer / L3 Switch DHCP Server Configuration

Karena multilayer switch berada pada Layer 2 dan Layer 3, ini mengizinkan untuk Switch tersebut melakukan DHCP Server layaknya Device Layer 3 seperti router. Dasar dari konfigurasi DHCP Server pada Multilayer Switch ini juga terbilang sama dengan konfigurasi DHCP Server pada Router.

Topologi:

![image-20230927201246770](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230927201246770.png)

VLAN Table:

| VLAN ID | Name   | Network         |
| ------- | ------ | --------------- |
| 10      | VLAN10 | 192.168.10.0/24 |
| 20      | VLAN20 | 192.168.20.0/24 |

Addressing Table:

| Device | IP Address / Prefix | Interface |
| ------ | ------------------- | --------- |
| L3SW   | 192.168.10.1/24     | vlan 10   |
|        | 192.168.20.1/24     | vlan 20   |
| PC1    | DHCP                | Fa0       |
| PC2    | DHCP                | Fa0       |

## Basic Multilayer / L3 Switch DHCP Server Configuration Steps

### Step 1 (L3SW VLAN Addressing)

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

# Tambahkan IP Address (to each SVI)
// VLAN10
L3SW(config)# interface vlan 10
L3SW(config-if)# ip address 192.168.10.1 255.255.255.0
L3SW(config-if)# description VLAN10 Network
L3SW(config-if)# exit

// VLAN20
L3SW(config)# interface vlan 20
L3SW(config-if)# ip address 192.168.20.1 255.255.255.0
L3SW(config-if)# description VLAN20 Network
L3SW(config-if)# exit

# Enable VLAN Access pada Interface
// VLAN10
L3SW(config)# interface fastEthernet 0/1
L3SW(config-if)# switchport mode access
L3SW(config-if)# switchport access vlan 10
L3SW(config-if)# exit

// VLAN20
L3SW(config)# interface fastEthernet 0/2
L3SW(config-if)# switchport mode access
L3SW(config-if)# switchport access vlan 20
L3SW(config-if)# exit
```

### Step 2 (Create DHCP Pool)

**L3SW>**

```markdown
# Create DHCP Pool
// VLAN10 DHCP
L3SW(config)# ip dhcp pool VLAN10-Network
L3SW(dhcp-config)# default-router 192.168.10.1
L3SW(dhcp-config)# network 192.168.10.0 255.255.255.0
L3SW(dhcp-config)# exit

// VLAN20 DHCP
L3SW(config)# ip dhcp pool VLAN20-Network
L3SW(dhcp-config)# default-router 192.168.20.1
L3SW(dhcp-config)# network 192.168.20.0 255.255.255.0
L3SW(dhcp-config)# exit
```

#### Verifikasi

```markdown
L3SW# show ip dhcp pool
L3SW# show running-config | sec dhcp
```

### Verifikasi

Akses pada PC1 dan PC2 kemudian aktifkan DHCP Addressing.

Cek DHCP Server apakah sudah berjalan



