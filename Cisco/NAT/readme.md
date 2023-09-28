# Apa Itu NAT?

NAT atau Network Address Translation adalah service yang bekerja pada router atau platform edge untuk menghubungkan private network ke public network seperti internet. Sesuai namanya, NAT merubah atau menerjemahkan IP Private ke IP Public. Dengan adanya NAT, sebuah organisasi hanya memerlukan satu IP Public yang merepresentasikan semua dari private network yang dimiliki untuk terhubung ke Network luar. 

NAT juga dapat memberikan sekuritas private pada Internal Network, karena secara tidak langsung Outside Network tidak bisa mengetahui apa yang terjadi dan ada apa saja pada Internal Network terkecuali terdapat pre-exiting NAT Translation. Oleh karena itu NAT dapat memisakan public dan private network.

# Bagaimana NAT Bekerja?

Secara umum, Router yang dikonfigurasi NAT memiliki satu interface di local (inside) network dan satu interface di global (outside) network. 

Berikut adalah tipe NAT yang paling sering digunakan yakni:

- Static NAT (SNAT)
- Dynamic NAT (DNAT)
- Port Address  Translation (PAT) / Dynamic NAT Overload
- NAT Overlapping

Referensi :

- https://www.cisco.com/c/en/us/products/routers/network-address-translation.html#~q-a

- https://www.trivusi.web.id/2022/08/network-address-translation.html
- https://www.geeksforgeeks.org/types-of-network-address-translation-nat/
- https://www.techtarget.com/searchnetworking/definition/Network-Address-Translation-NAT
- https://www.baguz.info/inet/pengertian-nat-jenis-cara-kerja-fungsi-manfaat/#3-overloading-nat
