# Apa itu Access Control List?

ACL atau Access Control List merupakan sebuah cara untuk melakukan Filtering atau mengontrol traffic yang ada pada sebuah jaringan. ACL memungkinkan pengguna untuk mengizinkan atau menolak paket dari suatu host yang menuju tujuan tertentu pada Interface di Router ataupun Switch. ACL bisa dikategorikan layaknya Firewall yang berisi daftar aturan atau Rule yang dibuat oleh administrator. 

(Masih Belum Lengkap)

Terdapat 2 jenis ACL yakni:

- Standar ACL
- Extended ACL

Sebelum bisa berfungsi dengan baik, access list harus diterapkan pada interface rotuer terlebih dahulu. Hal ini dikarenakan ACL bekerja dengan melakukan filtering traffic ketika sampai di interface Router atau Switch. Terdapat 2 penempatan yang bisa dipilih yakni:

- Inbound ACL (Paket yang masuk ke dalam interface)

  Ketika sebuah ACL diterapkan pada paket inbound disebuah Interface, paket tersebut diproses melalui ACL sebelum di-route ke outbound Interface. Setiap paket yang ditolak tidak bisa diroute karena paket ini diabaikan sebelum proses routing.

- Outbound ACL (Paket yang keluar dari Interface)

  Ketika sebuah ACL diterapkan pada paket outbound pada sebuah interface, paket tersebut di-route ke outbound interface dan diproses melalui ACL.

> Namun perlu diingat bahwa dalam menempatkan ACL, dalam satu interface hanya boleh ditempatkan Inbound atau Outbound.

```kotlin
// Sebagai Contoh Penempatan ACL
interface gigabitEthernet 0/1
ip access-group 10 inside/outside
```

Dalam pengimlemetasian ACL, ketika adanya paket yang tidak cocok dengan Rule atau List  ACL secara otomatis paket tersebut akan ditolak. Inilah yang disebut dengan Implicit Deny.

Membuat sebuah access list, access list dapat didefinisikan dengan menggunakan sistem penomoran atau menggunakan sistem penamaan 

- ACL Standard

  ```kotlin
  // Menggunakan Sistem Penomoran 
  Router(config)# access-list <1-99> <permit/deny> ...
  
  // Menggunakan Sistem Penamaan 
  Router(config)# ip access-list <standart> <nama-acl>
  Router(config-std-nacl)# permit host <host>
  ```

- ACL Extended

  ```kotlin
  // Menggunakan Sistem Penomoran 
  Router(config)# access-list <100-199> <permit/deny> ...
  
  // Menggunakan Sistem Penamaan 
  Router(config)# ip access-list <extended> <nama-acl>
  Router(config-std-nacl)# permit/deny host <host>
  ```

  

Referensi :

- https://www.geeksforgeeks.org/standard-access-list/
- https://miqbal.staff.telkomuniversity.ac.id/acl-access-list/
- https://www.diaryconfig.com/2018/09/mengenal-access-list-pada-cisco.html
- https://www.networkstraining.com/ccna-training-access-control-lists/

