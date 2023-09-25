# Apa itu VLAN?
**VLAN** atau **Virtual Local Area Network** secara garis besar bertugas untuk memisah membagi sebuah LAN menjadi beberapa kelompok. Ini bertujuan untuk menghemat penggunaan IP dan yang paling sering agar tidak menggunakan terlalu banyak Network Device untuk membangun sebuah jaringan.

# Apa itu Inter-VLAN Routing?

Seperti yang kita tahu, VLAN digunakan untuk segmentasi Layer 2 Network, karena alasan tersebut kita tidak bisa melakukan transaksi terhadap network yang berbeda segementasinya. Agar bisa saling berkomunikasi antar 2 segment tersebut, diperlukan L3 Switch atau Router untuk menyediakan Routing. Disinilah Inter-VLAN routing berperan, yaitu sebagai forwarder traffic dari satu VLAN ke VLAN lainnya

Secara umum terdapat 3 jenis Inter-VLAN Routing, diantaranya

- Legacy Inter-VLAN Routing
- Router-on-a-Stick
- Layer 3 Switch (SVI)

