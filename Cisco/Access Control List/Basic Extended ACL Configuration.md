# Basic Extended ACL Configuration

Extended ACL digunakan untuk melakukan filter secara lebih spesifik dan dalam Extended ACL kita bisa melakukan filter pada Source dan Destination yang dimana seperti kita tahu bahwa pada Standart ACL kita hanya bisa melakukan filtering pada Source packet saja. Extended ACL bisa melakukan filterisasi traffic berdasarkan protokolnya, semisal kita hanya ingin protokol ICMP saja yang di-drop, atau kita hanya mengizinkan protokol HTTP saja dan sebagainya. Sama seperti Standart ACL, kita bisa menggunakan Numbered Access List dan Named Access List.

Extended ACL menggunakan penomoran `100-199` dan biasanya diaplikasikan pada interface yang paling dekat dengan source.

Scenario Topology:

![image-20230929114134396](https://github.com/diotriandika/learn-networking/assets/109568349/a233628d-4d00-496a-ac17-285313adadeb)

Addressing Table :

| Device | IP Address / Prefix | Interface | Description              |
| ------ | ------------------- | --------- | ------------------------ |
| R1     | 202.10.17.1/30      | Gig 0/0   | Public Network           |
|        | 192.168.10.1/24     | Gig 0/1   | Gateway Holo Network     |
|        | 172.16.65.1/24      | Gig 0/2   | Gateway BTR Network      |
| R2     | 202.10.17.2/30      | Gig 0/0   | Public Network           |
|        | 10.10.10.1/29       | Gig 0/1   | Gateway SRV Network      |
| SRV1   | 10.10.10.5/29       | Fa0       | As Web Server            |
| SRV2   | 10.10.10.6/24       | Fa0       | As FTP Server            |
| Zeta   | 192.168.10.5/24     | Fa0       | permit HTTP, deny FTP    |
| Moona  | 192.168.10.6        | Fa0       | permit HTTP, deny FTP    |
| Ryo    | 172.16.65.5         | Fa0       | permit FTP, deny HTTP    |
| Kita   | 172.16.65.6         | Fa0       | permit ALL with SSH Line |

User SSH Credentials:

> Username : lnaerher
>
> Password : Skills39 (Keep Secret)
>
> Use full privilege

Task :

- Holo Network can't access FTP Server on SRV1
- Ryo can access FTP Server, but not the Web Server

- Kita can't'Access All SRV, but the only can (SSH) Remote Access to R1
- All other users available can't use Remote Access SSH

## Basic Extended ACL Configuration

### Step 1 (Addressing & Basic Configuration)

**R1>**

```kotlin
R1> enable
R1# configure terminal

// Tambahkan IP Address (ke Public Network)
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 202.10.17.1 255.255.255.252
R1(config-if)# description Public Network
R1(config-if)# no shutdown
R1(config-if)# exit

// Tambahkan IP Address (ke Holo Network)
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# description Default Gateway Holo Network
R1(config-if)# no shutdown
R1(config-if)# exit

// Tambahkan IP Address (ke BTR Network)
R1(config)# interface gigabitEthernet 0/2
R1(config-if)# ip address 172.16.65.1 255.255.255.0
R1(config-if)# description Default Gateway BTR Network
R1(config-if)# no shutdown
R1(config-if)# exit

// Buat User SSH 
R1(config)# username lnearher privilege 15 secret Skills39
```

**R2>**

```kotlin
R2> enable
R2# configure terminal

// Tambahkan IP Address (ke Public Network)
R2(config)# interface gigabitEthernet 0/0
R2(config-if)# ip address 202.10.17.1 255.255.255.252
R2(config-if)# description Public Network
R2(config-if)# no shutdown
R2(config-if)# exit

// Tambahkan IP Address (ke SRV Network)
R2(config)# interface gigabitEthernet 0/1
R2(config-if)# ip address 10.10.10.1 255.255.255.248
R2(config-if)# description Default Gateway SRV Network
R2(config-if)# no shutdown
R2(config-if)# exit
```

#### Verifikasi

Cek kembali konfigurasi dengan 

```kotlin
show running-config
```

Assign IP Address pada semua End Device sesuai dengan Addressing Table.

### Step 2 (Create Access List)

**R1>**

```kotlin
// Buat Access-List Extended (disini saya menggunakan sistem penamaan)
R1(config)# ip access-list extended RULE

// Buat Rule (ingat bahwa ACL membaca rule dari atas kebawah, jadi utamakan rule block terlebih dahulu)
// Holo Network Rule
R1(config-ext-nacl)# deny tcp 192.168.10.0 0.0.0.255 any eq 21 // Ini Rule Block FTP
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 80 // Ini Rule Allow WWW
// BTR Network Rule
R1(config-ext-nacl)# deny tcp host 172.16.65.5 any eq 80 // Ini Rule Block WWW
R1(config-ext-nacl)# permit tcp host 172.16.65.6 any eq 22 // Ini Rule Allow SSH
```

Jadi maksud dari konfigurasi diatas adalah sebagai berikut 

```kotlin
// Rule Holo 1
deny tcp 192.168.10.0 0.0.0.255 any eq 21
```

> Maksudnya, kita ***DENY/Tolak*** protokol ***TCP*** pada network ***192.168.10.0*** dengan wildcard mask ***0.0.0.255*** ke destinasi network ***ANY/Semua*** yang memiliki port ***Port 21*** (port 21 adalah port default untuk akses FTP)

```kotlin
// Rule Holo 2
permit tcp 192.168.10.0 0.0.0.255 any eq 80 
```

> Maksudnya, kita ***PERMIT/Izinkan*** protokol ***TCP*** pada network ***192.168.10.0*** dengan wildcard mask ***0.0.0.255*** ke destinasi network ***ANY/Semua*** yang memiliki ***Port 80*** (port 80 adalah port default untuk akses HTTP)

```kotlin
// Rule BTR 1
deny tcp host 172.16.65.5 any eq 80
```

> Maksudnya, kita ***DENY/Tolak*** protokol ***TCP*** pada host yang memiliki address ***172.16.65.5*** ke network destination ***ANY/Semua*** yang memiliki ***Port 80.***

```kotlin
// Rule BTR 2
permit tcp host 172.16.65.6 any eq 22
```

> Maksudnya, kita ***PERMIT/Izinkan*** protokol ***TCP*** pada host yang memiliki address ***172.16.65.5*** ke network destination ***ANY/Semua*** yang memiliki ***Port 22*** (port 22 adalah port default untuk akses SSH)

Dasarnya pada konfigurasi Extended NAT ini kita hanya perlu mendefinisikan

```kotlin
<permit/deny> <protocol> <source-host/network> <destination-host/network> <match-port-if-needed>
```

Jika belum paham bisa cek Web Ini :

- https://www.geeksforgeeks.org/extended-access-list/
- https://www.cisco.com/c/en/us/td/docs/app_ntwk_services/waas/waas/v401_v403/command/reference/cmdref/ext_acl.html

### Step 3 (Mengaplikasikan Extended ACL)

**R1>**

Sebelum itu, kita definisikan terlebih dahulu ACL akan diaplikasikan pada Inbound atau Outbound Interface, seperti yang kita tahu bahwa Extended ACL akan efektif jika kita aplikasikan pada Inbound (source). Namun karena topologi yang kita konfigurasi itu adalah memfilter traffic yang menuju SRV Network yakni Outbound, maka akan lebih mudah jika kita aplikasikan pada Outbound (Destination) Interface saja. Kalian bisa menggunakan Inbound (Source) yakni dengan mengaplikasikan Rule ACL pada kedua Inbound (Source) Interface yaitu Gig 0/1 dan Gig 0/2

```kotlin
// Tempatkan Rule Extended ACL pada Interface
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip access-group RULE out
```

### Verifikasi

Coba untuk cek apakah Holo Network bisa mengakses FTP atau tidak, begitu juga dengan PC-Kita. 

Lanjukanya dengan memverfikasi sesuai task.

Coba untuk mendisable ACL pada Interface tadi, dan lihat apa perbedaannya

```kotlin
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# no ip access-group RULE out
```

