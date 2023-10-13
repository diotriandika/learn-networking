# Karimata Web Service

**Tasks:**

Karimata Web Service

---

- Configure rote.lks.id as a web server serving all requests for all *.karimata.id websites.
- Serve www.karimata.id with the default html file.
  - Enable HTTPS using the Certificate Authority with wildcard domain *.karimata.id.
  - Redirect non-https request to https.
  - Put all website content in C:\webdir\www\
  - Create the necessary DNS record at Karimata DNS

---

### Step 1 - Installing IIS

Buka **Server Manager** kemudian select **Add New Roles and Features**

Next sampai di **Server Roles** dan check **Web Server (IIS)**, next lagi sampai di **Role Services** dan Check **Centralized SSL Certificate Support**

![image-20231012211350386](https://github.com/diotriandika/learn-networking/assets/109568349/81566cd7-2e1e-4cd3-9978-5f4818f1e3ec)


Install

### Step 2 - Issuing Certificate

Buka **IIS Manager**

Klik hostname, kemudian buka Server Certificates

![image-20231012211915091](https://github.com/diotriandika/learn-networking/assets/109568349/9b88d08c-c766-46eb-82ea-5515d6008f16)


Next select section `Create Certificate Request` pada tab **Actions**

![image-20231012212015402](https://github.com/diotriandika/learn-networking/assets/109568349/b880c684-9ef5-4763-8cef-db126b595949)


Isi sesuai dengan task lalu next

![image-20231013065141461](https://github.com/diotriandika/learn-networking/assets/109568349/40734410-0405-4757-9af8-ea6b6c03df25)


Specify direktori yang akan digunakan, disini secara otomatis file akan berformat **.txt**, namun pada linux biasanya berformat **csr** then finish

![image-20231013065225699](https://github.com/diotriandika/learn-networking/assets/109568349/a5262a42-c269-4c42-817c-eacce8432098)


### Step 3 - Signing Issued Certificate

Buka **Server Manager**, klik **Tools** lalu buka **Certification Authority**

Klik kanan pada `Karimata-RootCA` > **All Tasks** > **Submit new request...**

![image-20231012212606097](https://github.com/diotriandika/learn-networking/assets/109568349/7e266488-184e-4235-b84e-9c9e8222136b)


Cari issued ceritificate yang diekspor sebelumnya dan klik open

![image-20231013065250725](https://github.com/diotriandika/learn-networking/assets/109568349/495a3824-51ff-4af4-bc1a-31709329756f)


Cek pada section **Pending Requests** lalu klik kanan pada certificate yang diissue sebelumnya > **All Tasks** > **Issue**

![image-20231012212958884](https://github.com/diotriandika/learn-networking/assets/109568349/5245aeac-2f99-4368-942d-1ca728655afb)


Selanjutnya buka pada tab **Issued Certificates** dan double click pada ceritficate hingga muncul popup window baru. Pilih Tab **Details**, dan select **Copy to File**

![image-20231012213216926](https://github.com/diotriandika/learn-networking/assets/109568349/8ed05c6c-8724-41a8-b50c-bd56c4bd7130)


Ikuti wizard yang muncul, next aja sampe menu specify name & lokasi dari certificate yang akan diexport next trus hingga finish

![image-20231013065408242](https://github.com/diotriandika/learn-networking/assets/109568349/efdcc7b9-ee70-42a4-925b-613497d8dd09)


### Step 4 - Importing Certificate for SSL 

Buka **IIS Manager** 

Klik hostname, kemudian buka Server Certificates

![image-20231012211915091](https://github.com/diotriandika/learn-networking/assets/109568349/86e90e80-13de-4cb4-96d3-7e86d0ae027d)


Pada tab Actions, klik `Complete Certificate Request`

![image-20231013065700889](https://github.com/diotriandika/learn-networking/assets/109568349/c5401a71-4033-44cf-9657-8a955e051024)


Tambahkan lokasi file cretificate, kemudian tambahkan friendly name (disini sesuaikan dengan Task yaitu `*.karimata.id`) dan pastikan untuk meletakan certificate yang baru di **Web Hosting**

![image-20231013070235758](https://github.com/diotriandika/learn-networking/assets/109568349/711468e7-4b85-4bd4-87ba-0c76a7ec306f)


Sekarang akan muncul certificate baru yang bisa kita gunakan untuk binding SSL pada Web Service dengan domain `*.karimata.id`

![image-20231013070314743](https://github.com/diotriandika/learn-networking/assets/109568349/884e6915-7818-46de-b4ef-a2c35b603765)


### Step 5 - Enable SSL for Web Service

Buka **IIS Manager**

Pada menu sites buka `Default Web Site` (sesuai dengan soal) dan pilih **Bindings** pada tab **Actions** dikanan

![image-20231013070526771](https://github.com/diotriandika/learn-networking/assets/109568349/a28ab8d4-f015-4943-98e7-f720fcebd5cb)


Akan muncul popup window baru, klik add. Kemudian pada Window Add Site Binding, sesuaikan seperti dibawah

![image-20231013070639890](https://github.com/diotriandika/learn-networking/assets/109568349/9bd22d5b-a45c-4f7b-bfb6-1e576d3da283)


Sekarang karena pada soal kita diarahkan untuk menaruh content website ke direktori `C:\webdir\www` kita akan memindahakan index dari default site kesana

Selanjutnya coba untuk membuka web yang sudah diintergrasi SSL

### Step 6 - Auto Redirect HTTP to HTTPS

Install module HTTP Redirection 

![image-20231013084005444](https://github.com/diotriandika/learn-networking/assets/109568349/244e17d2-216a-4017-b40c-07deda1f33ee)


Buka **IIS Manger** dan pilih site yang ingin diaktifkan auto redirect

pilih module **HTTP Redirect**

![image-20231013084052291](https://github.com/diotriandika/learn-networking/assets/109568349/a69afaa0-0392-47e9-852c-d3b7c61dd5cb)


Ikuti seperti dibawah, lalu apply dan restart website

![image-20231013084112141](https://github.com/diotriandika/learn-networking/assets/109568349/052838f1-6d67-4e2a-8c3b-9046270b0a42)
