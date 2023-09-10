# Content : Konfigurasi DFS pada Windows Server
## **Apa itu DFS?**

**DFS** atau **Distributed File System** adalah sebuah organisasi logic yang menyediakan file sharing pada multiple servers menjadi lebih terstruktur. DFS dapat memberikan layanan replikasi dari sebuah server ke server lain, dalam kata lain ketika client ingin megakses file share pada server, akses file share akan dialihkan ke server terdekat.

**DFS** menggunakan protokol SMB yang dimana ini dapat memudahkan kita untuk terhubung ke berbagai SMB Share yang terdapat pada NAS dan Linux yang juga menggunakan SMB.

Kita bisa mengkonfigurasi satu DFS Share yang mana mencakup banyak file share dan menghubungkan user kesebuah file share (single path) yang menggunakan Unified Namespace. Ketika user terhubung ke file share menggunakan single path tersebut, user akan melihat struktur dari shared folder atau subfolder dan dapat mengakses file yang dibutuhkan. Dalam struktur DFS, terdapat dua komponen utama, yaitu DFS Namespace dan DFS Replication.

Reference :
- https://www.nakivo.com/blog/configure-dfs-replication-for-windows-server/
- https://www.techtarget.com/searchstorage/definition/distributed-file-system-DFS
- https://learn.microsoft.com/en-us/windows/win32/dfs/distributed-file-system-dfs-functions
