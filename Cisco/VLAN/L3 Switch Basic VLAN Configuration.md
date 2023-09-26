# L3 Switch Basic VLAN Configuration

Perbedaan mendasar dari L2 Switch dengan L3 Switch adalah L2 Switch beroperasi hanya dengan menyimpan MAC Address Table sedangkan multilayer Switch atau L3 Switch dapat menyimpan MAC Address dan Routing Table. Sehingga sangat memungkinkan untuk pengalamatan pada L3 Switch itu sendiri.

Konfigurasi L3 Switch kurang lebih sama seperti konfigurasi L2 Switch + Router. Hanya saja disini kita bisa menggunakan L3 Switch saja sebagai alat untuk Switching sekaligus untuk Addressing VLAN itu sendiri.

Topologi :

![image-20230925213125745](https://github.com/diotriandika/learn-networking/assets/109568349/c3cad239-82ec-46f1-bc6c-f9377b07d157)

VLAN Table:

| VLAN ID | Name   | Interface |
| ------- | ------ | --------- |
| 10      | VLAN10 | Fa0/1     |
| 20      | VLAN20 | Fa0/2     |

Addressing Table :

| Device    | IP Address   | Interface |
| --------- | ------------ | --------- |
| L3 Switch | 192.168.10.1 | VLAN 10   |
|           | 192.168.20.1 | VLAN 20   |
| PC1       | 192.168.10.2 | Fa0       |
| PC2       | 192.168.20.2 | Fa0       |

## L3 Switch VLAN Configuration Steps

### Step 1 (VLAN Addressing)

L3-Switch>

```markdown
Switch> enable
Switch# configure terminal

# Buat VLAN Table sesuai dengan Appendix
Switch(config)# vlan 10
Switch(config-vlan)# name VLAN10
Switch(config-vlan)# vlan 20
Switch(config-vlan)# name VLAN20
Switch(config-vlan)# exit

# Tambahkan IP Address pada masing - masing SVI
# VLAN 10
Switch(config)# interface vlan 10
Switch(config-if)# description VLAN 10 Default Gateway (optional)
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# exit

# VLAN 20
Switch(config)# interface vlan 20
Switch(config-if)# description VLAN 20 Default Gateway (optional)
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# exit
```

```markdown
# Definisikan port yang akan digunakan untuk VLAN Access
# VLAN 10
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

# VLAN 20
Switch(config)# interface fastEthernet 0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit
```

### Verifikasi

```C++
Switch# show vlan
Switch# show running-config | sec vlan
```

Coba untuk ping ke L3 Switch
Coba untuk ping ke PC-2 dan sebaliknya

