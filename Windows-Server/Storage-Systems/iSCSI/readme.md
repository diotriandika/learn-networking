# Content : Konfigurasi iSCSI Storage Pada Windows Server
**iSCSI (Internet Small Computer System Interface)** merupakan protokol yang bekerja di (transport layer) sebagai media penyimpanan yang menggunakan protokol IP sebagai penghubung diantara storage dan server. iSCSI dibuat untuk memungkinkan untuk menyediakan shared-storage berbasis jaringan yang dimana terdapat banyak server dan client yang mengakses central storage.

Dalam pengimplementasikan di Windows Server, terdapat _iSCSI Target Server_ yang bertugas untuk menyediakan Image/Disk yang nantinya akan dapat diakses melalui jaringan oleh _iSCSI Initiator_. Secara gamblang, bisa dibilang ini adalah Disk yang bersifat virtual.
