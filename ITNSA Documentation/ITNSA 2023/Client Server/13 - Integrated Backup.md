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

![image-20231015195145100](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015195145100.png)

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

![image-20231015211059589](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015211059589.png)

Fill nama dan pilih `Run whether user is logged on or not` pada section **Security Options**

![image-20231015211210469](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015211210469.png)

Buka tab **Triggers**, select **New...** dan isi sesuai dengan dibawah lalu Ok

![image-20231015211318249](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015211318249.png)

Buka tab **Actions**, select **New..** 

Disini saya akan buat scedule untuk backup ke jukung.lks.id folder `C:/backup`

![image-20231015211554392](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015211554392.png)

> Program/script : `wbadmin`
>
> Add arguments :
>
> ```
> start backup -backupTarget:\\10.196.10.10\c-backup -include:C\internal -quiet
> ```

Selanjutnya create new lagi untuk buat schedule untuk bakcup ke komodo.lks.id folder `F:/backup`

![image-20231015212104524](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015212104524.png)

Sekarang kita memiliki dua task backup, klik Ok untuk close window

![image-20231015212027241](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015212027241.png)

Jika muncul popup seperti dibawah, masukan saja password dari Administrator

![image-20231015212204766](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015212204766.png)

Akan muncul task baru yang sudah kita buat sebelumnya. Akan tetapi, task ini belum berjalan. Klik kanan pada task dan select Run untuk menjalankan task

![image-20231015212325755](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015212325755.png)

![image-20231015213002087](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20231015213002087.png)

Coba untuk hapus file dan restore

Refrence:

- https://practical365.com/schedule-windows-server-backup-exchange-2010/
- https://www.ubackup.com/windows-server/windows-server-backup-multiple-schedules-4348.html
- https://serverfault.com/questions/617438/windows-server-backup-fails-there-is-not-enough-disk-space-to-create-the-volume

## Configure DFS-Replication

