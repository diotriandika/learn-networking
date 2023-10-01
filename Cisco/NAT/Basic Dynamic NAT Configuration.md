# Basic Dynamic NAT Configuration

Kurang lebih Dynamic NAT mirip dengan Static NAT, hanya saja pada pemetaan IP Private ke IP Public Dynamic NAT menggunakan lebih dari 1 IP Public. Seperti yang kita tahu bahwa Static NAT memetakan IP dengan one-to-noe mapping, sedangkan Dynamic NAT memetakan IP Private dengan lebih dari 1 IP Public (pool) yang dimana IP Private dimapping dengan IP Public secara acak dalam interval waktu. Secara tidak langsung memang ini memberikan kita keamanan lebih, namun juga dapat menyebabkan borosnya penggunaan IP Public.

Referensi :

- https://www.baguz.info/inet/pengertian-nat-jenis-cara-kerja-fungsi-manfaat/#3-overloading-nat
- https://study-ccna.com/dynamic-nat/
- geeksforgeeks.org/dynamic-nat-configuration-in-cisco/
- chatGPT :)

Topologi:

![image-20231001135008485](https://github.com/diotriandika/learn-networking/assets/109568349/e577b732-6b0e-45da-8e38-927212bf2fcf)

Addressing Table:

| Device  | IP Address / Prefix | Interface | Description                    |
| ------- | ------------------- | --------- | ------------------------------ |
| R1      | 202.10.17.1/28      | Gig 0/0   | Public                         |
|         | 192.168.10.1/24     | Gig 0/1   | Default Gateway Home Network   |
| R2      | 202.10.17.2/28      | Gig 0/0   | Public                         |
|         | 10.10.10.1/24       | Gig 0/1   | Default Gateway Office Network |
| SRV-1   | 10.10.10.10/24      | Fa0       | Web Server                     |
| SRV-2   | 10.10.10.11/24      | Fa0       | FTP Server                     |
| Home-PC | 192.168.10.10/24    | Fa0       | LAN Client                     |



## Basic Dynamic NAT Configuration Steps

### Step 1 (Addressing & Definisikan Interface Inside dan Outside pada R2)

**R1>**

```kotlin
R1> enable
R1# configure terminal

// Tambahkan IP Address (to Public)
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 202.10.17.1 255.255.255.240
R1(config-if)# description to Public Network
R1(config-if)# no shutdown
R1(config-if)# exit

// Tambahkan IP Address (to Home)
R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# description to Home Network
R1(config-if)# no shutdown
R1(config-if)# exit

// Tambahkan Default Route
R2(config)# ip route 0.0.0.0 0.0.0.0 202.10.17.2
```

**R2>**

```kotlin
R2> enable
R2# configure terminal

// Tambahkan IP Address (to Public)
R2(config)# interface gigabitEthernet 0/0
R2(config-if)# ip address 202.10.17.2 255.255.255.240
R2(config-if)# description to Public Network
R2(config-if)# ip nat outside
R2(config-if)# no shutdown
R2(config-if)# exit

// Tambahkan IP Address (to Office)
R2(config)# interface gigabitEthernet 0/1
R2(config-if)# ip address 10.10.10.1 255.255.255.0
R2(config-if)# description to Office Network
R2(config-if)# ip nat inside
R2(config-if)# no shutdown
R2(config-if)# exit

// Tambahkan Default Route
R2(config)# ip route 0.0.0.0 0.0.0.0 202.10.17.1
```

### Step 2 (Buat Access List)

**R2>**

```kotlin
// Buat Standart ACL untuk listing Source (Inside) Pool IP
R2(config)# access-list 10 permit 10.10.10.0 0.0.0.255
```

### Step 3 (Buat NAT Pool)

> NAT Pool ini terdiri dari kumpulan IP Public yang nantinya akan digunakan sebagai mapping dari IP Private ke Public

**R2>**

```kotlin
// Buat NAT Pool dengan nama bebas dan Start IP dari 202.10.17.10 hingga End IP 202.10.17.14
R2(config)# ip nat pool Office-Pool 202.10.17.10 202.10.17.14 netmask 255.255.255.240
```

### Step 4 (**Enable** Dynamic NAT)

**R2>**

```kotlin
// Aktifkan Dynamic NAT dengan menggunakan ACL & NAT Pool yang sudah dibuat sebelumnya
R2(config)# ip nat inside source list 10 pool Office-Pool
```

### Verifikasi

Cek Translasi IP, dan coba lakukan ping dari Home-PC

```kotlin
R2# show ip nat translation
```

?? Ya intinya pokoknya ntr SRV-1 & SRV-2 bakal secara random ngambil IP Public yang tersedia. Yang booting duluan bakal dapat IP Pertama, dan berlanjut. Ini secara ga langsung membuat device lebih aman ketimbang 
