# Basic NAT Overload (PAT) Configuration

NAT Overload atau sering disebut PAT (Port Address Translation) merupakan salah satu tipe dari Dynamic NAT akan tetapi cara kerjanya adalah menggabungkan seluruh IP pada Private Network menjadi satu IP Public. Hal ini dapat berjalan karena setiap Client dengan IP Private dipisahkan atau diberikan Port yang berbeda di belakang IP Address Public.

Referensi :

- https://www.baguz.info/inet/pengertian-nat-jenis-cara-kerja-fungsi-manfaat/#3-overloading-nat
- https://www.trivusi.web.id/2022/08/network-address-translation.html


```kotlin
// Define the Outside and Inside Interface
interface Inside Local
ip nat inside

Interface Outside Local
ip nat outside

// Create ACL
access-list 10 permit <source-network> <wildcard-mask>

// Overloading NAT
ip nat inside source list 10 interface <outside-interface> overload
```
