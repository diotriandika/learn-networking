# Basic Configuration Static Routing

Topologi:

![image-20230926205100129](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230926205100129.png)

Addressing Table:

| Device | IP Address / Prefix | Interface |
| ------ | ------------------- | --------- |
| R1     | 10.10.10.1/30       | Gig 0/0   |
|        | 192.168.10.1/24     | Gig 0/1   |
| R2     | 10.10.10.2/30       | Gig 0/0   |
|        | 192.168.20.1/24     | Gig 0/1   |
| PC0    | 192.168.10.2/24     | Fa0       |
| PC1    | 192.168.20.2/24     | Fa0       |

## Static Routing Configuration Steps

### Step 1 (Addressing Router)

**R1>**

```markdown
R1> enable
R1# configure terminal

# Tambahkan IP Address Local Area (ke PC0)
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# description Default Gateway PC0
R1(config-if)# no shutdown
R1(config-if)# exit

# Tambhakan IP Address Public Area (ke R2)
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 10.10.10.1 255.255.255.252
R1(config-if)# description Public Network
R1(config-if)# no shutdown
R1(config-if)# exit
```

**R2>**

```markdown
R2> enable
R2# configure terminal

# Tambahkan IP Address Local Area (ke PC0)
R2(config)# interface gigabitEthernet 0/1
R2(config-if)# ip address 192.168.20.1 255.255.255.0
R2(config-if)# description Default Gateway PC1
R2(config-if)# no shutdown
R2(config-if)# exit

# Tambhakan IP Address Public Area (ke R2)
R2(config)# interface gigabitEthernet 0/0
R2(config-if)# ip address 10.10.10.2 255.255.255.252
R2(config-if)# description Public Network
R2(config-if)# no shutdown
R2(config-if)# exit
```

#### Verifikasi 

Tambahkan IP Address pada PC0 dan PC1 (Pastikan untuk menggunakan IP Router sebagai Default Gateway)

Coba lakukan ping dari PC0 ke PC1. Tidak berhasil? Tentu karena R1 sama sekali tidak mengenal Network dari R2 begitu juga sebaliknya. Agar bisa saling terhubung, kedua Router harus memiliki Routing Table yang dimana nantinya keduanya bisa saling melakukan transaksi melalui jalur terdekat.

Secara kasar Routing disini berperan agar PC0 bisa saling terhubung dengan PC1 melalui Network Public / Public Area.

### Step 3 (Static Routing)

Selanjutnya adalah mengkonfigurasi Routing dengan menambahkan Routing Table secara manual atau disebut Static Routing. Formatnya adalah

```C++
Router(config)# ip route <destination-network> <destination-network-subnet> <via-shortest-address>
```

Jika implementasi dari Topologi kita akan menjadi seperti dibawah

**R1>**

```c++
R1(config)# ip route 192.168.20.0 255.255.255.0 10.10.10.2
```

lakukan juga pada R2

**R2>**

```markdown
R2(config)# ip route 192.168.10.0 255.255.255.0 10.10.10.1
```

#### Verifikasi

untuk melihat Routing Table yang sudah kita konfigurasi, jalankan perintah ini

```markdown
R1/R2# show ip route
```

**R1>**

```C
R1#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.10.10.0/30 is directly connected, GigabitEthernet0/0
L       10.10.10.1/32 is directly connected, GigabitEthernet0/0
     192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.10.0/24 is directly connected, GigabitEthernet0/1
L       192.168.10.1/32 is directly connected, GigabitEthernet0/1
S    192.168.20.0/24 [1/0] via 10.10.10.2
```

**R2>**

```c
R2#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.10.10.0/30 is directly connected, GigabitEthernet0/0
L       10.10.10.2/32 is directly connected, GigabitEthernet0/0
S    192.168.10.0/24 [1/0] via 10.10.10.1
     192.168.20.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.20.0/24 is directly connected, GigabitEthernet0/1
L       192.168.20.1/32 is directly connected, GigabitEthernet0/1
```

Coba lakukan ping kembali dari PC0 ke PC1 dan sebaliknya