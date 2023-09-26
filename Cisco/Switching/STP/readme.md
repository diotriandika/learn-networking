# Apa itu STP (Spanning Tree Protocol)?
STP atau Spanning Tree Protocol adalah sebuah protokol yang berada di jaringan switch yang dimanan bertugas untuk menghindari terjadinya frame looping ketika mengimplementasikan topologi redudansi. STP juga biasanya diguankan dalam jaringan yang menggunakan dua atau lebih jalur untuk menuju ke satu tujuan yang sama. STP melakukan monitoring pada network dan memastikan bahwa tidak terjadi loop dengan cara mematikan atau Blocking salah satu link redudant. Secara default, STP aktif pada semua port dan secara otomatis membuat salah satu switch menjadi Root Bridge. 

Root Bride dari Spanning Tree adalah bridge dengan ID terkecil atau terendah. Tiap bridge mempunyai Unique Identifier (ID) dan sebuah priority number yang bisa dikonfigurasi. Root Bridge ini berperan sebagai penentu dari arahnya paket / jalur network yang ada.



Referensi Lengkap :

- https://ngoprek-netpro.blogspot.com/2015/07/pengertian-dan-cara-kerja-spanning-tree.html
- https://www.techtarget.com/searchnetworking/definition/spanning-tree-protocol#:~:text=Spanning%20Tree%20Protocol%20(STP)%20is,LAN)%20that%20contains%20redundant%20paths.
- https://www.geeksforgeeks.org/root-bridge-election-in-spanning-tree-protocol/
- https://citraweb.com/artikel_lihat.php?id=226
- https://www.ciscopress.com/articles/article.asp?p=2832407&seqNum=4 (bagus)
