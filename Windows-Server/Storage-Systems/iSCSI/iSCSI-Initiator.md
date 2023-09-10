# Konfigurasi iSCSI Initiator pada Windows Server
iSCSI Initator disini berperan sebagai penghubung yang nantinya dapat menggunakan Virtual Disk yang disediakan oleh [iSCSI Target Server](https://github.com/diotriandika/learn-networking/blob/main/Windows-Server/Storage-Systems/iSCSI/iSCSI-Target-Server.md)

Required:
- [x] IP Configured
- [x] Hostname Set
- [x] Connected to iSCSI Target Server
> Optional:
> - Basic Configuration
> - as Domain Controller of lks.id 
## Instalasi iSCSI Initator
1. Pertama masuk ke Server Manager dan select Add new roles and features
   ```bash
   # Page pertama skip aja
   # Installation type pilih Role-Based
   Installation Type > Role-based or feature-based installation
   ```
2. Pilih server pada server pool
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/cfef9100-fd06-40dd-8958-d1542d6fee29)
3. Server Roles mark iSCSI Target Server di File and Storage Services
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/92cceb82-2d49-48e3-a438-f947511286ca)
4. Features biarkan lalu next
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e64bc8a5-8826-4981-8140-7566d108db98)
5. Install feature dan tunggu hingga selesai kemudian close
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/485fbfd6-3823-45d2-83f6-857a7bab5825)
## Konfigurasi iSCSI Initiator
1. Masuk ke menu Tools dan pilih option iSCSI Initiator
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/78754055-70f8-499e-bf97-11a775cf0b2f)
2. Jika keluar menu seperti dibawah, select yes.
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/5b75c91c-5638-4bf0-838e-5dcc629c83b5)
3. Masukan IP dari iSCSI Target Server pada tab target dan klik Quick Connect 
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e1fa80a9-ddd6-4839-939b-b1566bfcd53d)
4. Disini status dari Target Server sudah connected (jika menggunakan CHAP Authentication select Connect pada menu yang dihighlight, kemudan pilih Advanced, dan mark Enable CHAP Log ON)
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/8972fc84-74ad-49b8-a00c-9878813e138e)
## Setup Virtual Disk
1. Masuk ke Disk Management dengan klik kanan pada icon Windows
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/0e5794aa-d888-4f89-b570-fed983cb2e8f)
2. Akan muncul disk baru dengan status Offline dan Unallocated.
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/671018ab-a6f6-4e81-91a4-6b84b7e6112d)
3. Pertama kita rubah status Offline disk ke Online, caranya Right-Click di Disk dan select Online.
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d90da3a8-ba9e-4356-adf5-1a9588dc5eeb)
4. Statusnya akan berubah ke Not Initialized, Right-Click kembali di Disk tersebut dan select Initialize Disk
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/bc3ad665-07bb-4821-9f81-36c5c83f49ad)
5. Akan muncul window seperti dibawah, kemudian pilih disk yang akan diinitalize serta sesuaikan partition style (disini saya menggunakan MBR) lalu klik OK
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ead4bbf9-0592-4beb-aeac-c6ed937f6253)
6. Statusnya berubah kembali menjadi Online, namun disknya belum teralokasikan dan belum bisa diakses.
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/f0835a8a-9143-42cd-91a0-e0a20b572bb1)
7. Right-Click pada disk kemudian pilih New Simple Volume
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/9a623d18-7048-46d2-a79e-64a8f1a88d84)
8. Ikuti Menu Wizard, selanjutnya tentukan alokasi size dari volume.
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/9d4117dd-fe7b-43b5-a144-03e0c6f5538a)
9. Assign Letter dari Drive
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/52adcb1a-5cb4-4ebb-a322-b69376b2eeeb)
10. Format Partition sesuai kebutuhan lalu next dan finish
    > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/cb215a3c-596a-4447-aeb6-7a9bff432c71)
11. Pada Window Disk Management, Disk barusan sudah berubah statusnya menjadi online dan sudah bisa digunakan
    > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/387a40bf-a82e-41a4-a713-59afbaa11347)
## Verifikasi
Cek kembali dengan membuka file explorer dan coba untuk membuat file/folder di disk tersebut
![image](https://github.com/diotriandika/learn-networking/assets/109568349/69eb59c7-20f7-4803-80ac-d98f551491c4)

Referensi :
- https://computingforgeeks.com/how-to-configure-iscsi-initiator-in-windows-server/?expand_article=1
- https://www.server-world.info/en/note?os=Windows_Server_2019&p=iscsi&f=4
- https://www.sqlshack.com/how-to-configure-an-iscsi-initiator-on-windows-server-2016/
- https://softwarekeep.com/help-center/how-to-create-partitions-on-windows-10
