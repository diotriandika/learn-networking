# Basic DHCP Server (Router Dynamic Addressing) Configuration

DHCP atau (Dynamic Host Configuration Protocol) Server adalah sebuah protokol yang memungkinkan server untuk mendistribusikan alamat IP dan informasi konfigurasi secara dinamis kepada client.

Topology:

![image-20230927190317562](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230927190317562.png)

 Addressing Table

| Device  | IP Address / Prefix | Interface | Information      |
| ------- | ------------------- | --------- | ---------------- |
| Router0 | 192.168.10.1/24     | Gig 0/0   | DHCP Server LAN1 |
| PC0     | DHCP                | Fa0       | DHCP Client      |
| PC0     | DHCP                | Fa0       | DHCP Client      |

## Basic DHCP Server Configuration Steps

### Step 1 (Router Addressing)

**Router>**

```markdown
Router> enable
Router# configure terminal

# Tambahkan IP Address (to LAN1)
Router(config)#interface gigabitEthernet 0/0
Router(config-if)#ip address 192.168.10.1 255.255.255.0
Router(config-if)#description Default Gateway LAN1
Router(config-if)#no shutdown
Router(config-if)#exit
```

### Step 2 (Create DHCP Pool)

**Router>**

```markdown
# Buat DHCP Pool
Router(config)#ip dhcp pool LAN1
Router(dhcp-config)#default-router 192.168.10.1
Router(dhcp-config)#network 192.168.10.0 255.255.255.0
Router(dhcp-config)#exit
```

> Keterangan:
>
> - LAN1 : Merupakan parameter untuk NAMA dari DHCP Pool kita, bebas diganti dengan sesuka hati
> - default-router : Merupakan Default Gateway yang akan diberikan pada DHCP Client nantinya
> - network : Merupakan network yang akan kita pakai untuk DHCP Server.
>
> Ada juga parameter lain yang bisa digunakan, seperti:
>
> - dns-server : Merupakan parameter untuk Default DNS Server yang akan diberikan kepada DHCP Client.
> - domain-name : Merupakan parameter untuk nama domain default yang akan diberikan kepada DHCP Client
> - no <option> : merupakan parameter untuk menghapus informasi terkait <option> yang ingin dihilangkan. Contohnya jika ingin menghapus default gateway
>
> ```
> Router(dhcp-config)# no default-router
> ```

### Verifikasi

Coba cek ke PC0 dan PC1 dan set IP Addressing ke DHCP agar tahu apakah DHCP Server berjalan atau tidak.