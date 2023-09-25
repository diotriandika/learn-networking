# Basic VLAN Configuration

**VLAN** atau **Virtual Local Area Network** secara garis besar bertugas untuk memisah membagi sebuah LAN menjadi beberapa kelompok. Ini bertujuan untuk menghemat penggunaan IP dan yang paling sering agar tidak menggunakan terlalu banyak Network Device untuk membangun sebuah jaringan.

Sebagai contoh, disini saya menggunakan Cisco Packet Tracer sebagai perangkat simulasi dengan Topologi :

<img width="624" alt="image" src="https://github.com/diotriandika/learn-networking/assets/109568349/6b9d70dc-eee0-4629-826b-42226bad12a4">

VLAN Table :

| VLAN ID | Name     | Interfaces | Network          |
| ------- | -------- | ---------- | ---------------- |
| 91      | Manager  | Fa0/2      | 192.168.0.0/26   |
| 92      | Engineer | Fa0/3      | 192.168.0.64/26  |
| 93      | Support  | Fa0/4      | 192.168.0.128/27 |

Addressing Table

| Device | IP Address / Prefix | Interface  |
| ------ | ------------------- | ---------- |
| Router | 192.168.0.1/26      | Gig 0/1.91 |
|        | 192.168.0.65/26     | Gig 0/1.92 |
|        | 192.168.0.129/27    | Gig 0/1.93 |
| PC-0   | 192.168.0.2/26      | Fa0        |
| PC-1   | 192.168.0.66        | Fa0        |
| PC-2   | 192.168.0.130       | Fa0        |

## VLAN Configuration Steps 

### Step 1 (VLAN Addressing)

**Router>**

```markdown
Router> enable
Router# configure terminal

# Aktifkan Interface yang akan digunakan sebagai Sub-Interface (VLAN)
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# no shutdown
Router(config-if)# exit

# Tambahkan IP pada Sub-Interface VLAN91
Router(config)# interface gigabitEthernet 0/0.91
Router(config-subif)# encapsulation dot1Q
Router(config-subif)# ip address 192.168.0.1 255.255.255.192
Router(config-subif)# exit

# Tambahkan IP pada Sub-Interface VLAN92
Router(config)# interface gigabitEthernet 0/0.92
Router(config-subif)# encapsulation dot1Q
Router(config-subif)# ip address 192.168.0.65 255.255.255.192
Router(config-subif)# exit

# Tambahkan IP pada Sub-Interface VLAN93
Router(config)# interface gigabitEthernet 0/0.93
Router(config-subif)# encapsulation dot1Q
Router(config-subif)# ip address 192.168.0.129 255.255.255.224
Router(config-subif)# exit
```

### Verifikasi

Untuk melihat hasil konfigurasi, jalankan perintah berikut

```markdown
Router# show running-config | sec Gig
```

Tambahkan IP pada PC-0 - PC-2 sesuai Addressing Table, kemudian coba lakukan ping ke router. 

Tentu ping tidak akan bisa berjalan karena switch saat ini masih belum terkonfigurasi untuk akses informasi VLAN dari Router

### Step 2 (Mengaktifkan VLAN pada Switch Layer 2)

**Switch>**

```markdown
Switch> enable
Switch# configure terminal

# Buat VLAN Table sesuai dengan Appendix
Switch(config)# vlan 91
Switch(config-vlan)# name Manager
Switch(config-vlan)# vlan 92
Switch(config-vlan)# name Engineer
Switch(config-vlan)# vlan 93
Switch(config-vlan)# name Support
Switch(config-vlan)# exit

# Definisikan port yang terhubung ke router sebagai Trunk Port yang membawa semua informasi VLAN
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit

# Definisikan port yang akan digunakan untuk VLAN
# VLAN91
Switch(config)# interface fastEthernet 0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 91
Switch(config-if)# exit

# VLAN92
Switch(config)# interface fastEthernet 0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 91
Switch(config-if)# exit

# VLAN93
Switch(config)# interface fastEthernet 0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 91
Switch(config-if)# exit
```

### Verifikasi

```
Switch# show vlan
```

Coba ping kembali sekarang.
