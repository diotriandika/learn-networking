# Konfigurasi iSCSI Target Server
Konfigurasi & Setup iSCSI Target Server disini bertujuan untuk sebagai server penyedia virtual disk yang nantinya dapat diakses melalui protokol iSCSI.

Required :
- [x] IP Configured
- [x] Hostname Set
- [x] Joined domain to Initiator
- [x] Have at least 1 more disks to share
## Instalasi iSCSI Target  
1. Pertama masuk ke Server Manager dan select Add Roles dan Features
   ```bash
   # Page pertama Skip aja

   # Installation Type pilih role based
   Installation Type > Role Based
   ```
2. Pilih Server pada server pool
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ce40fc6b-083f-449c-ac23-8e87a48a2f0d)
3. Server Roles pilih iSCSI Target di File and Storage Services
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/7725d48c-7dd4-4f7f-88a6-7cf066f75b41)
4. Features biarkan
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a9dd02e0-c6b2-4819-bd92-3c1c5cbf52fd)
5. Install Feature dan tunggu sampai selesai lalu close
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/f5f711dd-7557-4429-84f9-f46eff613e4a)
## Konfigurasi iSCSI Target Server
1. Masuk ke File and Storage Services lalu pilih menu iSCSI
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/6d1c59f3-fba2-4e46-9f76-30ef094242dc)
2. Selanjutnya setup dengan klik text biru ditengah `To create an iSCSI virtual disk, start the New iSCSI Virtual Disk Wizard`
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/aa6a74b9-01bc-491b-b2b4-81eec3d89242)
3. Akan muncul menu seperti dibawah, disini kita akan memilih disk yang akan digunakan nantinya, atau bisa juga dengan custom path yang disertai dengan folder yang mana ini menjadi lebih terkelola.
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/50af6f5e-6a6a-489e-a804-17bf7c125fa1)
3. Buat nama untuk disk iSCSI
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/968c2c3f-d722-4d7d-b4a0-cda9f42d8f05)
4. Tentukan alokasi data disk yang akan digunakan, sebagai contoh disini saya menggunakan hanya 2GB dan menggunakan opsi Dynamic Expanding yang mana nantinya saya dapat merubah dari size disk saya.
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/872eea5f-6748-4e14-bd47-78dd46a0c23c)
5. Select New iSCSI Target
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/4fc94d37-2c41-4930-a4c0-3a08b61412df)
6. Tambahkan nama sebagai indikasi iSCSI Target
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d64cf4bb-b5ce-4588-9125-04bc283562b1)
7. Select Add
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/791390f0-6b68-49af-b34e-be1706dce585)
8. Pilih menu ke 3 dan rubah Type menjadi IP Address, ini ditunjukan untuk mengidentifiasi Initiator nantinya, atau bisa juga browse langsung melalui query jika sudah 1 domain.
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/fa173d88-2df6-4c82-85f0-6b21923d266c)
9. Masukan IP Address dari Server Initiator lalu OK
   > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/06e6c076-a52d-4842-8cb6-e7efc30c63a4)
11. Disini akan muncul Address yang sudah kita assign
    > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/198e37b6-8c48-494a-ab2a-f029c290709b)
12. Setup Authentication CHAP (Opsional)
    > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/df186a45-3fe4-41bd-876c-d879ed94f3fd)
13. Konfirmasi & Review kembali konfigurasi jika dirasa sudah benar klik Create
    > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/1a80b3e1-8fd2-456f-84b6-89f53b767f47)
15. Tunggu hingga proses konfigurasi selesai, dan close
    > ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e6313ba6-d204-43f8-88fc-cb89a2115df1)
## Verifikasi
Disini terdapat 1 Virutal disk yang sudah diassgin sebelumnya. Statusnya masih Not Connected yang mana ini berarti Virtual Disk masih belum terkoneksi dengan Initiator.
![image](https://github.com/diotriandika/learn-networking/assets/109568349/14bb87e0-eeca-4b76-8cfb-21f974297641)

Sekarang Virtual Disk sudah bisa digunakan oleh Initator, [cek disini]()

Referensi :
- https://computingforgeeks.com/install-and-configure-iscsi-target-on-windows-server/?expand_article=1
- https://green.cloud/docs/how-to-install-iscsi-target-on-windows-server-2019/



