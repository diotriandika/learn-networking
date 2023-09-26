# Apa itu FHRP?
FHRP (First Hop Redudancy Protocol)

FHRP adalah suatu protokol yang berguna untuk network agar selalu dalam kondisi aktif dengan cara menyediakan jalur Redudanscy pada dua atau lebih perangkat yang di konfigurasi menjadi satu perangkat virutal, salah satu perangkat akan menjadi jalur Active (utama) dan yang lain Standby atau jalur cadangan (Backup Link). Apabila jalur utama down, maka jalur yang masih aktif akan mengambil alih (Failover). 

Secara lengkap bisa cek disini:

- https://belajarcomputernetwork.com/2013/01/01/fhrp-first-hop-redundancy-protocol/
-  https://www.studocu.com/id/document/universitas-muhammadiyah-yogyakarta/teknologi-informasi/protokol-routing-pada-jaringan-komputer-ch9/44963405

- https://study-ccna.com/cisco-fhrp-explained/

Terdapat 3 cara untuk implementasi dari FHRP, diataranya:

- Hot Standby Router Protocol (HSRP)
- Virtual Router Redundancy Protocol (VRRP)

- Gateway Load Balancing Protocol (GLBP)
