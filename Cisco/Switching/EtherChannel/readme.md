# Apa itu EtherChannel?
EtherChannel secara sederhana adalah menggabungkan beberapa Interface/link menjadi satu Interface. Gabungan beberapa interface atau bisa dikatakan channel group tadi akan menjadi logical interface baru yang bernama port-channel.

EtherChannel digunakan uuntuk meningkatkan koneksi antar switch, router ataupun server. Jika salah satu interface dalam channel group rusak, maka channel group akan tetap bekerja menggunakan interface lain dalam channel group tersebut

Maksimum, EtherChannel dapat menggabungkan hingga 8 Interface sekaligus

Dalam EtherChannel terdapat 2 protocol yang sering digunakan, yaitu PAgP (Port Aggregation Protocol) dan LACP (Line Aggregation Control Protocol)

- https://www.ciscopress.com/articles/article.asp?p=2348266&seqNum=3
