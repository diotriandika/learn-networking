# Setup Domain Control pada Windows Server
Secara umum, Domain Controller adalah server yang bertugas untuk merespons otentikasi, manajemen pengguna, manajemen keamanan, dan manajemen sumber daya di seluruh jaringan yang menggunakan Active Directory.

Configured:
- [x] IP Address
- [x] Hostname
## Instalasi Domain Controller
1. Masuk ke Server Manager dan pilih `Add Roles and Features` (Default Tab `before you begin` bisa langsung diskip dan langsung ke tab selanjutnya)
   ```bash
   # Pilih installation type role based
   Installation Type > Role-based or feature-based installation
   ```
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/3829c5d8-0d26-4a3b-bc2e-e8d61b0b1953)

2. Pilih server yang akan digunakan untuk Domain Controller & Active Directory
   ```bash
   Server Selection > <your-server>
   ```
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/caeddd4d-4c7d-461d-9e19-2de921035809)

3. Pada Tab Server Roles pilih `Active Directory Domain Services` dan klik Add Features kemudian next
   ```bash
   Server Roles > Active Directory Domain Services
   ```
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/dcdff4ea-c27f-417f-a7ed-ab436bb19aef)

4. Tab dibawah next aja
   
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ffad3e72-1e18-48ae-a3d7-2808c90d9729)

6. Install Roles dan tunggu hingga proses instalasi selesai
   
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a310d10f-81d4-4dbf-8e65-bc269982cda6)
## Konfigrurasi Domain Controller
Disini sudah terlihat bahwa Active Directory dan Domain Controller sudah terpasang yang diindikasikan dengan adanya menu baru pada Roles & Server Groups
![image](https://github.com/diotriandika/learn-networking/assets/109568349/01fa0170-6b30-4c6e-93c7-c5aac59a6f77)

Selanjutnya adalah mengkonfigurasi Domain Controller
1. Select tanda Warning pada Menu pojok kanan atas, dan pilih `Promote this server to a domain controller`
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/fffb89a2-3269-471e-967c-d4a434edd931)
2. Pada menu pertama (Deployment Configuration) silahkan pilih deployment operation yang dibutuhkan, disini saya akan menggunakan server ini sebagai DC utama jadi saya memilih `Add a new forest` dan menambahkan domain baru.
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ebe23302-41a0-4ea9-866c-e3c5033b8471)
3. Selanjutnya menu (Domain Controller Options) biarkan default dan tambahkan Recovery Password (DSRM) untuk mengubah & mereset DC.
   
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ad250bf6-8f29-46b6-b501-b360b914af61)
5. **(DNS Option) bisa langsung diskip dengan unmark (biarkan kosong) pada `Create DNS Delegation`**
6. (Additional Option) biarkan default menyesuaikan dengan domain
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/d1bf6f8a-3154-4140-a150-2f99b6eed7d8)
7. **Untuk (Paths) biarkan default**
8. (Review Option) Review kembali konfigurasi, jika dirasa sudah benar klik next
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/702e349d-f7cb-4f4e-8fc6-aee7c2c818c2)
9. (Prequisites Check) jika sudah memenuhi ketentuan, selanjutnya select Install
   ![image](https://github.com/diotriandika/learn-networking/assets/109568349/44f81449-9d02-4fb6-bcd0-5f3ac7942871)
10. Jika proses Instalasi Selesai, secara otomatis server akan langsung melakukan rebooting dan tunggu hingga booting kembali
    ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e88a01f4-6781-4cd4-a5b9-3dd693abda15)
## Verifikasi
User Login akan berubah seperti contoh dibawah
![image](https://github.com/diotriandika/learn-networking/assets/109568349/5968a7cb-3079-4c75-b9c2-f6daf0be7793)

Cek Kembali di server manager
![image](https://github.com/diotriandika/learn-networking/assets/109568349/8ff5b27e-808a-4d4e-a9f2-151471dbfaf8)


   




