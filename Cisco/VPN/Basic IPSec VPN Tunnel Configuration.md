# Basic IPSec VPN Tunnel Configuration

> Karena sedikitnya informasi terkait dengan IPsec, jadi saya jelaskan disini secara general.

Internet Protocol Security (IPSec) adalah kumpulan protokol keamanan jaringan yang menyediakan keamanan dalam berkomunikasi melalui jaringan IP seperti Internet. Terdapat banyak hal yang bisa kita lakukan ketika menggunakan protokol ini, seperti VPN Tunneling, Site-to-site VPN, Remote Access VPN, Secured VoIP, dan lain - lain. 
IPSec VPN Tunneling

Lebih jelasnya bisa cek :

- https://www.cisco.com/c/en/us/support/docs/routers/1700-series-modular-access-routers/71462-rtr-l2l-ipsec-split.html

- https://www.cisco.com/c/en/us/td/docs/security/vpn_modules/6342/vpn_cg/6342site3.html#wp1035810
- https://www.ciscopress.com/articles/article.asp?p=24833&seqNum=3
- https://www.ciscopress.com/articles/article.asp?p=24833&seqNum=6

- dancourses youtube

Topologi:



Addressing Table:

| Device     | IP Address / Prefix | Interface | Description                    |
| ---------- | ------------------- | --------- | ------------------------------ |
| Home       | 202.10.17.2/30      | Gig 0/0   | to Public                      |
|            | 192.168.10.1/30     | Gig 0/1   | Default Gateway Home Network   |
| ISP        | 202.10.17.1/30      | GIg 0/0   | Public                         |
|            | 202.20.17.1/30      | Gig 0/1   | Public                         |
| Office     | 202.20.17.2/30      | Gig 0/0   | to Public                      |
|            | 172.16.65.1/28      | Gig 0/1   | Default Gateway Office Network |
| Home-PC    | 192.168.10.254      | Fa0       | Remote                         |
| Office-SRV | 172.16.65.10        | Fa0       | Access Server                  |

## Basic IPSec VPN Tunnel Configuration Steps

### Step 1 (Addressing & Basic Configuration)

**Home>**

```kotlin
Home> enable
Home# configure terimnal

// Tambahkan IP Address (to Public)
Home(config)# interface gigabitEthernet 0/0
Home(config-if)# ip address 202.10.17.2 255.255.255.252
Home(config-if)# description to Public Network
Home(config-if)# no shutdown
Home(config-if)# exit

// Tambahkan IP Address (to Home Network)
Home(config)# interface gigabitEthernet 0/1
Home(config-if)# ip address 192.168.10.1 255.255.255.0
Home(config-if)# description Default Gateway Home Network
Home(config-if)# no shutdown
Home(config-if)# exit

// Buat Default Route
Home(config)# ip route 0.0.0.0 0.0.0.0 202.10.17.1
```

**ISP>**

```kotlin
Home> enable
Home# configure terimnal

// Tambahkan IP Address (to Home Router)
Home(config)# interface gigabitEthernet 0/0
Home(config-if)# ip address 202.10.17.1 255.255.255.252
Home(config-if)# description to Home Router
Home(config-if)# no shutdown
Home(config-if)# exit

// Tambahkan IP Address (to Office Router)
Home(config)# interface gigabitEthernet 0/1
Home(config-if)# ip address 202.20.17.1 255.255.255.252
Home(config-if)# description to Office Router
Home(config-if)# no shutdown
Home(config-if)# exit
```

**Office>**

```kotlin
Home> enable
Home# configure terimnal

// Tambahkan IP Address (to Public)
Home(config)# interface gigabitEthernet 0/0
Home(config-if)# ip address 202.20.17.2 255.255.255.252
Home(config-if)# description to Public Network
Home(config-if)# no shutdown
Home(config-if)# exit

// Tambahkan IP Address (to Office Network)
Home(config)# interface gigabitEthernet 0/1
Home(config-if)# ip address 172.16.65.1 255.255.255.240
Home(config-if)# description Default Gateway Office Network
Home(config-if)# no shutdown
Home(config-if)# exit

// Buat Default Route
Home(config)# ip route 0.0.0.0 0.0.0.0 202.20.17.1
```

#### Verifikasi

Cek kembali konfigurasi IP

Assign Address pada Home-PC dan Office-SRV sesuai dengan Appendix

### Step 2 (Enable Security License)

**Home>**

```kotlin
// Cek apakah Router memiliki Module Security License atau tidak
Home# show version
...
Technology Package License Information for Module:'c2900'

----------------------------------------------------------------
Technology    Technology-package          Technology-package
              Current       Type          Next reboot
-----------------------------------------------------------------
ipbase        ipbasek9      Permanent     ipbasek9
security      securityk9    Evaluation    securityk9
uc            disable       None          None
data          disable       None          None

// Cek pada Tab Technology, lihat Technology-Package dari Security. Jika yang muncul seperti diatas itu artinya Security License sudah terpasang.
```

**Office>**

```kotlin
// Cek apakah Router memiliki Module Security License atau tidak
Office# show version
...
Technology Package License Information for Module:'c2900'

----------------------------------------------------------------
Technology    Technology-package          Technology-package
              Current       Type          Next reboot
-----------------------------------------------------------------
ipbase        ipbasek9      Permanent     ipbasek9
security      None          None          None
uc            None          None          None
data          None          None          None
```

Diatas terlihat bahwa status Technology-package dari Security statusnya masih None, ini berarti router tidak memiliki module Security License.

Selanjutnya kita tambahkan dengan cara sebagai berikut

**Office>**

```kotlin
Office# configure terminal
Office(config)# license boot module c2900 technology-package securityk9
```

Jika muncul pertanyaan masukan Yes kemudian Enter

Selanjutnya save konfigurasi dan lakukan reboot

```kotlin
Office(config)# do write
Offic(config)# do reload
```

#### Verifikasi

Pastikan Router yang akan menggunakan VPN Tunneling sudah aktif security licensenya.

```kotlin
<hostname># show version
...
Technology Package License Information for Module:'c2900'

----------------------------------------------------------------
Technology    Technology-package          Technology-package
              Current       Type          Next reboot
-----------------------------------------------------------------
ipbase        ipbasek9      Permanent     ipbasek9
security      securityk9    Evaluation    securityk9
uc            disable       None          None
data          disable       None          None

```

### Step 3 (Create ACL to Permit Traffic)

**Home>**

```kotlin
// Buat Extended ACL dengan Rule Permit Home Network untuk mengakses Office Network.
Home(config)# access-list 100 permit ip 192.168.10.0 0.0.0.255 172.16.65.0 0.0.0.15
```

**Office>**

```kotlin
// Buat Extended ACL dengan Rule Permit Office Network untuk mengakses Home Network.
Office(config)# access-list 100 permit ip 172.16.65.0 0.0.0.15 192.168.10.0 0.0.0.255
```

### Step 4 (PHASE 1 - Setup ISAKMP Policy & Key)

**Home>**

```kotlin
// Konfigurasi ISAKMP Policy dengan Priority bebas
Home(config)# crypto isakmp policy 25
// Aktifkan Pre-share key Authentication
Home(config-isakmp)# authentication pre-share
// Set Algoritma Encryption dengan AES 256
Home(config-isakmp)# encryption aes 256
// Gunakan Key exchange Diffie-Hellman group 5
Home(config-isakmp)# group 5
Home(config-isakmp)# exit

// Set ISAKMP Key dengan password Skills39 dan Peer Address yakni IP dari Outside Interface Router Office
Home(config)# crypto isakmp key Skills39 address 202.20.17.2
```

**Office>**

```kotlin
// Konfigurasi ISAKMP Policy dengan Priority bebas
Office(config)# crypto isakmp policy 25
// Aktifkan Pre-share key Authentication
Office(config-isakmp)# authentication pre-share
// Set Algoritma Encryption dengan AES 256
Office(config-isakmp)# encryption aes 256
// Gunakan Key exchange Diffie-Hellman group 5
Office(config-isakmp)# group 5
Office(config-isakmp)# exit

// Set ISAKMP Key dengan password Skills39 dan Peer Address yakni IP dari Outside Interface Router Home
Office(config)# crypto isakmp key Skills39 address 202.10.17.2
```

### Step 5 (PHASE 2 - IPsec Transform Set)

**Home>**

```kotlin
// Konfigurasi IPSec Transform Set dengan Tag Home-to-Office, ESP AES encryption 256 Key, dan HMAC-SHA Auth key hash.
Home(config)# crypto ipsec transform-set Home-to-Office esp-aes 256 esp-sha-hmac
```

**Office>**

```kotlin
// Konfigurasi IPSec Transform Set dengan Tag Office-to-Home, ESP AES encryption 256 Key, dan HMAC-SHA Auth key hash.
Office(config)# crypto ipsec transform-set Office-to-Home esp-aes 256 esp-sha-hmac
```

### Step 6 (Setup Crypto Map)

**Home>**

```kotlin
// Buat Crypto Map dengan Tag IPSec-Map, sequence entry bebas, dan gunakan ipsec-isakmp
Home(config)# crypto map IPSec-Map 10 ipsec-isakmp

// Set Peer Address, yakni sama dengan ISAKMP Key Peer Address 
Home(config-crypto-map)# set peer 202.20.17.2

// Set PFS yakni sama dengan ISAKMP Policy yang menggunakan Diffie-Hellman group 5
Home(config-crypto-map)# set pfs group5

// Set security-association dengan lifetime maksimum
Home(config-crypto-map)# set security-association lifetime seconds 86400

// Set transform-set yakni Tag dari Tranfrom Set pada STEP 5
Home(config-crypto-map)# set transform-set Home-to-Office

// Enable ACL yang sudah dibuat
Home(config-crypto-map)# match address 100
Home(config-crypto-map)# exit
```

**Office>**

```kotlin
// Buat Crypto Map dengan Tag IPSec-Map, sequence entry bebas, dan gunakan ipsec-isakmp
Office(config)# crypto map IPSec-Map 10 ipsec-isakmp

// Set Peer Address, yakni sama dengan ISAKMP Key Peer Address 
Office(config-crypto-map)# set peer 202.10.17.2

// Set PFS yakni sama dengan ISAKMP Policy yang menggunakan Diffie-Hellman group 5
Office(config-crypto-map)# set pfs group5

// Set security-association dengan lifetime maksimum
Office(config-crypto-map)# set security-association lifetime seconds 86400

// Set transform-set yakni Tag dari Tranfrom Set pada STEP 5
Office(config-crypto-map)# set transform-set Office-to-Home

// Enable ACL yang sudah dibuat
Office(config-crypto-map)# match address 100
Office(config-crypto-map)# exit
```

### Step 7 (Apply Crypto Map to Interface)

**Home>**

```kotlin
// Aplikasikan Crypto-Map yang sudah dibuat ke Outside Interface Router
Home(config)# interface gigabitEthernet 0/0
Home(config-if)# crypto map IPSec-Map
Home(config-if)# exit
```

**Office>**

```kotlin
// Aplikasikan Crypto-Map yang sudah dibuat ke Outside Interface Router
Office(config)# interface gigabitEthernet 0/0
Office(config-if)# crypto map IPSec-Map
Office(config-if)# exit
```

### Verifikasi

Coba lakukan ping dari Home-PC ke Office-SRV

> Memang terjadi delay yang cukup lama yang menyebabkan Request timed out dalam beberapa packet, namun akan normal kembali.

