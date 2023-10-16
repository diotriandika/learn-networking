# Intergrated Backup

**Tasks:**

---

- Use Windows Backup to backup C:\internal\cert from rote.lks.id to these destinations daily at any hour:
  - F:\backup at komodo.lks.id
  - C:\backup at jukung.lks.id
- Execute the backup at least once to have immediate backup.
- Use DFS-Replication or something to keep these folders synchronized:
  - F:\backup at komodo.lks.id
  - F:\backup at jukung.lks.id

---

## Configure Windows Backup

### Step 1 - Installing Windows Backup

Buka **Server Manager**, add New Roles and Features

```bash
Server Roles  > Skip
Features > Windows Server Backup
Install
```

Buka Menu **Tools** dan cari **Windows Server Backup** lalu buka

Selanjutnya pilih menu **Local Backup** dan tunggu initiasi selesai

Jika sudah tampilan akan berubah seperti dibawah

![image-20231015195145100](https://github.com/diotriandika/learn-networking/assets/109568349/e3dbd2e0-67df-4f60-a040-c78544f395ae)


Selanjutya kita bisa melakukan backup dengan menggunakan Wizard, namun jika menggunakan wizard kita hanya bisa melakukan satu task backup. Contohnya seperti dibawah

 Menggunakan **Backup Schedule** pada tab **Actions**

```bash
Getting Started > Skip
Select Backup Configuration > Custom
Select Items for Backup > Add Items > (pilih folder C:/internal/cert)
Specify Backup Time > Once a Day
Specify Destination Type > Back up to a shared network folder 
Specify Remote Shared Folder > \\10.196.10.11\backup
Confirmation > Finish
```

Jika kita mencoba untuk membuka Backup Schedule kembali, kita hanya bisa mengedit task sebelumnya, oleh karena itu saya akan menggunakan **Task Scheduler**.

### Step 2 - Scheduling with Task Scheduler

Buka windows search dan cari **Task Scheduler** kemudian open

Select **Create Task** pada tab **Actions**,

![image-20231015211059589](https://github.com/diotriandika/learn-networking/assets/109568349/46c77783-9c03-4d35-91d7-559139156c91)


Fill nama dan pilih `Run whether user is logged on or not` pada section **Security Options**

![image-20231015211210469](https://github.com/diotriandika/learn-networking/assets/109568349/bed11dbe-4334-4aa2-83d3-7b813da21bfd)


Buka tab **Triggers**, select **New...** dan isi sesuai dengan dibawah lalu Ok

![image-20231015211318249](https://github.com/diotriandika/learn-networking/assets/109568349/bc65fb3e-3974-45f5-a074-1179efb60409)

Buka tab **Actions**, select **New..** 

Disini saya akan buat scedule untuk backup ke jukung.lks.id folder `C:/backup`

![image-20231015211554392](https://github.com/diotriandika/learn-networking/assets/109568349/21cd9c45-6c5f-4c79-9964-e393796f0714)


> Program/script : `wbadmin`
>
> Add arguments :
>
> ```
> start backup -backupTarget:\\10.196.10.10\c-backup -include:C\internal -quiet
> ```

Selanjutnya create new lagi untuk buat schedule untuk bakcup ke komodo.lks.id folder `F:/backup`

![image-20231015212104524](https://github.com/diotriandika/learn-networking/assets/109568349/895c4b1c-5955-400b-a1b6-7d7c96fa5b10)


Sekarang kita memiliki dua task backup, klik Ok untuk close window

![image-20231015212027241](https://github.com/diotriandika/learn-networking/assets/109568349/508a5ad9-a5f2-4dd3-be08-9c62e3a401c9)


Jika muncul popup seperti dibawah, masukan saja password dari Administrator

![image-20231015212204766](https://github.com/diotriandika/learn-networking/assets/109568349/9ec0d383-32a1-48a6-86a0-668835d6e852)


Akan muncul task baru yang sudah kita buat sebelumnya. Akan tetapi, task ini belum berjalan. Klik kanan pada task dan select Run untuk menjalankan task

![image-20231015212325755](https://github.com/diotriandika/learn-networking/assets/109568349/8623e900-b112-4e6f-a123-99bd24637104)


![image-20231015213002087](https://github.com/diotriandika/learn-networking/assets/109568349/ea1e5c54-51db-44b4-925d-ee84295627fb)


Coba untuk hapus file dan restore

Refrence:

- https://practical365.com/schedule-windows-server-backup-exchange-2010/
- https://www.ubackup.com/windows-server/windows-server-backup-multiple-schedules-4348.html
- https://serverfault.com/questions/617438/windows-server-backup-fails-there-is-not-enough-disk-space-to-create-the-volume

## Configure DFS-Replication

