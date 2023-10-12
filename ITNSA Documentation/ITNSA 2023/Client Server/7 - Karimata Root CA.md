# Karimata Root Certificate

**Tasks:**

---

Use rote.lks.id as the certificate issuer for all services.

- Configure as Root CA.
- Use Common Name: Karimata-RootCA.



---

### Step 1 - Install Certification Authority

Buka **Server Manager**, pilih **Add Roles and Features**

Skip semua sampe di **Server Roles** dan check pada roles **Active Directory Ceritificate Services**

![image-20231012201526486](https://github.com/diotriandika/learn-networking/assets/109568349/1b352b8b-cb88-450e-96e6-83c2e3a09f0a)


Next skip lagi sampe **Role Services** kemudian hanya check pada section **Certification Authority**

![image-20231012201644351](https://github.com/diotriandika/learn-networking/assets/109568349/5a8cd9a2-d5cf-4cb9-a875-ec13615c31e0)


Install

### Step 2 - Setup Certificate Authority

Setelah installasi, akan muncul notif yang menyarankan konfigurasi AD-CS pada localhost. Klik highlighted text

![image-20231012202110649](https://github.com/diotriandika/learn-networking/assets/109568349/1f29942d-e21c-4216-b040-e8d738f40809)


Next sampe di **Role Services** check **Certification Authority** lalu next dan pilih **Standalone CA**

**CA Type** pilih **Root CA** next pilih **Create a new private key** kemudian next sampai **CA Name**

Isi Common name sesuai dengan Task

![image-20231012202424964](https://github.com/diotriandika/learn-networking/assets/109568349/11a3c243-e5c9-4deb-8645-125ffb967d4f)


Next sampai selesai

### Step 3 - Export Root CA

Buka windows search, cari `Manage computer certificates`

Buka folder **Personal**, lalu klik kanan pada **Root Ca** yang sudah dibuat tadi, pilih **All Tasks** dan **Export**

![image-20231012202712126](https://github.com/diotriandika/learn-networking/assets/109568349/d5f99a92-5953-48bd-a703-d8d39f3653ce)


Muncul Wizard 

![image-20231012202905174](https://github.com/diotriandika/learn-networking/assets/109568349/d323f9ab-99eb-4e1c-bf3d-96651d21484d)

Next aja sampe di window **File to Export**. Select Browse dan taruh Root CA sesuai dengan Tasks

![image-20231012203226772](https://github.com/diotriandika/learn-networking/assets/109568349/d013ce3f-a8df-48e1-b84a-3d7197755517)


next hingga finish

