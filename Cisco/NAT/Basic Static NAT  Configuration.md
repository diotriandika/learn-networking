# Basic Static NAT  Configuration 

Static NAT (Network Address Translation) adalah translasi dengan cara pemetaan one to one dari alamat IP Private ke IP Public meski IP tersebut belum didaftarkan. Maksud dari one to one mapping disini adalah 1 Alamat IP Private akan ditraslasikan menjadi 1 Alamat IP Public. Hal ini dibutuhkan ketika adanya Server spesifik lokal yang ingin diakses dari Public Network. 

Pada static NAT ada beberapa hal yang perlu diperhatikan yakni:

- NAT inside interface - Layer 3 Interface yang mengarah ke Private Network
- NAT outside interface - Layer 3 Interface yang mengarah ke Public Network
- Local Address - Alamat yang terdapat pada private network
- Global Address - Alamat yang terdapat pada public network

Referensi : 

- https://www.ciscopress.com/articles/article.asp?p=25273&seqNum=4
- YouTube dancourses
- chatGPT :)

Topologi:

![image-20230928093902037](https://github.com/diotriandika/learn-networking/assets/109568349/1c4c87b5-529c-4815-88d2-c212ccc7b68c)

Addressing Table:

| Device         | IP Address / Prefix | Interface    |
| -------------- | ------------------- | ------------ |
| R1             | 202.10.17.1/24      | Serial 0/0/0 |
|                | 172.16.65.1/24      | Gig 0/0      |
| R2             | 202.10.17.2/24      | Serial 0/0/0 |
|                | 192.168.10.2/24     | Gig 0/0      |
| SRV            | 172.16.65.10/24     | Fa0          |
| PC1            | 192.168.10.2/24     | Fa0          |
| SRV-Translated | 202.10.17.10        | NAT          |

## Basic Static NAT Configuration

### Step 1 (Addressing & Basic Configuration)

**R1>**

```kotlin
R1> enable
R1# configure terminal

// Konfigurasi IP Address (to Public)
R1(config)# interface serial 0/0/0
R1(config-if)# description Interface Connected to Public
R1(config-if)# ip address 202.10.17.1/24
R1(config-if)# exit

// Konfigurasi IP Address (to Private)
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# description Interface Connected to Private
R1(config-if)# ip address 172.16.65.1/24
R1(config-if)# exit

// Tambahkan Default Route atau kalian bisa menggunakan Static Route, OSPF dkk
R1(config)# ip route 0.0.0.0 0.0.0.0 202.10.17.2
```

**R2>**

```kotlin
R2> enable
R2# configure terminal

// Konfigurasi IP Address (to Public)
R1(config)# interface serial 0/0/0
R1(config-if)# description Interface Connected to Public
R1(config-if)# ip address 202.10.17.2/24
R1(config-if)# exit

// Konfigurasi IP Address (to Private)
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# description Interface Connected to Private
R1(config-if)# ip address 192.168.10.1/24
R1(config-if)# exit

// Tambahkan Default Route atau kalian bisa menggunakan Static Route, OSPF dkk
R1(config)# ip route 0.0.0.0 0.0.0.0 202.10.17.1
```

#### Verifikasi

Cek kembali IP dengan `show running-config`

Tambahkan IP Address untuk SRV dan PC1

### Step 2 (NAT Configuration)

**R1>**

```kotlin
// Definisikan NAT Inside dan NAT Outside
// NAT Inside (Interface Private Network)
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip nat inside
R1(config-if)# exit

// NAT Outside (Interface Public Network)
R1(config)# interface serial 0/0/0
R1(config-if)# ip nat outside
R1(config-if)# exit
```

Translasikan IP Private menjadi Public dengan format

```kotlin
ip nat inside source static <ip-private> <ip-public>
```

Dalam kasus ini, kita ingin merubah IP Private `172.16.65.10` menjadi `202.10.17.10` 

```kotlin 
// Konfigurasi Static NAT
R1(config)# ip nat inside source static 172.16.65.10 202.10.17.10
```

#### Verifikasi Konfigurasi

Sekarang coba untuk melakukan ping ke 172.16.65.10

Outputnya yang Reply itu pasti bukan `172.16.65.10` akan tetapi `202.10.17.10`, ini berarti Static NAT berhasil, ini membuat Device yang menggunkan Static NAT menjadi lebih aman. Coba juga untuk melakukan ping ke `202.10.17.10`

```kotlin
// Verifikasi dengan
R1(config)# show ip nat translation

Pro  Inside global     Inside local       Outside local      Outside global
---  202.10.17.10      172.16.65.10       ---                ---
tcp 02.10.17.10:80     02.10.17.10:80    192.168.10.10:1038 192.168.10.10:1038
```

### Tip

Kita juga bisa mencoba mengakses server lain dengan IP dengan contoh SRV2

```kotlin
// Tambahkan Port untuk Akses Web Server
R1(config)# ip nat inside source static tcp 172.16.65.20 80 202.10.17.10 80
```

IP dari `172.16.65.20` kita anggap sebagai SRV2, jadi ketika kita mengakses  IP 202.10.17.10 dengan port 80, secara otomatis kita akan diarahkan ke SRV2
