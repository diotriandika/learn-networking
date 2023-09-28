# Basic Standart ACL Configuration

Standart ACL hanya menggunakan Source IP Address untuk mengontrol dan memberikan rule permit atau deny. Standart ACL digunakan untuk mengontrol lalu lintas berdasarkan alamat IP sumber dalam jaringan. Standard ACL hanya memiliki kemampuan untuk memeriksa alamat IP sumber dan memutuskan apakah lalu lintas tersebut diizinkan atau ditolak berdasarkan alamat IP tersebut. Mereka tidak dapat memeriksa alamat IP tujuan, nomor port, atau parameter lain dalam header paket. Contoh umum dari penerapan Standart ACL adalah memblok sebuah host atau network agar tidak bisa berkomunikasi dengan network lain.

ACL Standart menggunakan penomoran `1-99` dan biasanya diletakan pada interface yang paling dekat dengan destination packet

```kotlin
// Format ACL Penomoran
Router(config)# access-list <1-99> <permit-deny> 

// Format ACL Penamaan
Router(config)# ip access-list standart <nama-acl>
Router(config-std-nacl)# permit ...
```

Referensi :

- https://www.geeksforgeeks.org/standard-access-list/

- https://www.diaryconfig.com/2018/09/mengenal-access-list-pada-cisco.html
- https://miqbal.staff.telkomuniversity.ac.id/acl-access-list/
- https://ilmiteknik.co.id/konfigurasi-access-list-standard-pada-cisco

Topology Scenario:

![image-20230928233907030](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230928233907030.png)

Addressing Table:

| Device | IP Address / Prefix | Interface | Description  |
| ------ | ------------------- | --------- | ------------ |
| Router | 10.10.10.1/24       | Gig 0/0   | SRV Network  |
|        | 192.168.10.1/24     | Gig 0/1   | Holo Network |
|        | 172.16.65.1/24      | Gig 0/2   | BTR Network  |
| SRV    | 10.10.10.10/24      | Fa0       | permit ACL   |
| Moona  | 192.168.10.5/24     | Fa0       | permit ACL   |
| Kobo   | 192.168.10.6/24     | Fa0       | deny ACL     |
| Zeta   | 192.168.10.7/24     | Fa0       | permit ACL   |
| Kita   | 172.16.65.5/24      | Fa0       | deny ACL     |
| Ryo    | 172.16.65.6/24      | Fa0       | deny ACL     |

Task :

- Deny Kobo from accessing SRV
- Deny BTR Network from accessing SRV
- Permit all other user available

## Basic Standart ACL Configuration Steps

### Step 1 (Addressing)

**Router>**

```kotlin
Router> enable
Router# configure terminal

// Tambahkan IP Address (ke SRV Network)
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 10.10.10.1 255.255.255.0
Router(config-if)# description Default Gateway SRV Network
Router(config-if)# no shutdown
Router(config-if)# exit

// Tambahkan IP Address (ke HOLO Network)
Router(config)# interface gigabitEthernet 0/1
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# description Default Gateway HOLO Network
Router(config-if)# no shutdown
Router(config-if)# exit

// Tambahkan IP Address (ke BTR Network)
Router(config)# interface gigabitEthernet 0/2
Router(config-if)# ip address 172.16.65.1 255.255.255.0
Router(config-if)# description Default Gateway BTR Network
Router(config-if)# no shutdown
Router(config-if)# exit
```

Tambahkan IP Address pada semua End Device sesuai dengan Addressing Table

### Step 2 (Create Standart ACL)

**Router>**

```kotlin
// Buat Access-List Standart (disini saya menggunakan sistem penamaan)
Router(config)# ip access-list standart BLOCK

// Tambahkan Rule Deny untuk host Kobo
Router(config-std-nacl)# deny host 192.168.10.6

// Tambahkan Rule Permit Untuk HOLO Network
Router(config-std-nacl)# permit 192.168.10.0 0.0.0.255
Router(config-std-nacl)# exit
```

> Seperti yang kita tahu, bahwa jika kita membuat ACL, secara otomatis akan memblock semua network yang tidak memiliki kecocokan pada Rule ACL. Ini disebut dengan *Implicit Deny*

**Router>**

```kotlin
// Definisikan Outbound Interface untuk meletakan ACL
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip access-list BLOCK out 
```

> Kenapa kita meletakan Rule ACL pada gigabitEthernet 0/0?
>
> Ini karena sesuai dengan sifat dari ACL, akan lebih baik dan efektif jika kita meletakan Rule ACL pada interface yang mengarah ke *Destination Packet.*

### Verifikasi

Cek Rule ACL yang sudah kita buat dengan menjalankan 

```kotlin
Router# show access-list
```

> Note lagi, perlu diingat bahwa dalam ACL memiliki sifat untuk membaca Rule dari atas ke bawah, jadi berhati-hatilah untuk mengkonfigurasi Rule ACL.
>
> Dan jika kalian membuat Rule ACL dengan Numbered (penomoran) kalian tidak bisa menghapus satu Rule. Kita perlu menhapus Group ACL tersebut. Hal ini berbeda dengan Named, karena kita bisa menghapus salah satu Rule dengan parameter `no` 

Coba lakukan ping dari PC Zeta dan Moona ke SRV dan coba untuk melakukan ping dari BTR Network ke SRV.