# Objective
![image](https://github.com/diotriandika/learn-networking/assets/109568349/20342478-9831-4256-a0e6-36607d2af450)
# Penyelesaian Task 1
1. Tambahkan disk di VM, jika menggunakan virtualbox setup seperti dibawah.
   - Pilih VM yang mau diedit, kemudian masuk menu `Settings`
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/24f23c62-1096-4ff5-bb83-1b2053387d3b)
   - Pilih tab `Storage`, selanjutnya klik ikon + di window tengah seperti dibawahdan pilih harddisk.
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/517a9005-2916-40dd-88fc-59eb985d06f8)
   - Create Virtual Hard Disk, Type = VDI, Pre-allocate Full Size, Allocate size Virtual Disk (dalam kasus ini buat 2GB) Terakhir finish.
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/0f265fc4-2076-4223-90fa-f8f3a83a462a)
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/1d44d68f-5817-4dd3-919e-8980fbbfd3b5)
   - Select Virtual Disk yang sudah dibuat kemudian pilih choose.
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/e4cda432-5a0a-4bfa-b0c7-eb7b4f059ef9)
# Penyelesaian Task 2
masuk superuser/root `sudo -i`
1. Membuat partisi
   - gunakan `parted -l` untuk mengecek partisi apa sudah ada apa belum
   - pastikan /dev/sdc sudah ada
   - buat partisi menggunakan `parted /dev/sdc`
   - source : https://phoenixnap.com/kb/linux-create-partition
   - tambahkan label, `mklabel gpt`
   - review partition table, `print`
   - buat partisi `mkpart primary <type> <start-size> <end-size>`
   - review kembali partition table
   - save dengan enter syntax `quit`
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/629c1eaf-d854-4746-8ad9-345a4e4c473a)
   - source : https://phoenixnap.com/kb/linux-create-partition
   - cek kembali partisinya menggunakan `lsblk -o name,mountpoint,label,size,uuid`
   - source : https://unix.stackexchange.com/questions/14165/list-partition-labels-from-the-command-line
     > Jika salah input size partisi/mau reallocate partisi, bisa delete dulu partisinya.
     > https://www.thegeekdiary.com/how-to-delete-disk-partition-using-parted-command/
# Penyelesaian Task 3
1. Labeling Partition
   - berikan label `appcache` pada partisi `/dev/sdc1` menggunakan e2label/tune2fs
   - `e2label /dev/sdc1 appcache` / `tune2fs -L appcache /dev/sdc1`
   - cek kembali label partisi menggunakan `lsblk`
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/640bade6-8635-4a23-a01a-600a85f8cbcb)
   - source : https://www.tecmint.com/change-modify-linux-disk-partition-label-names/
# Penyelesaian Task 4
1. Mounting filesystem `appcache` ke direktori `/app`
   - Buat mountpoint menggunakan `mkdir`
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/cee34d33-1bee-4132-876a-f6b8b460f4ba)
   - Mount `appcache` ke mountpoint
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a73e36b4-c406-44ca-8034-fc829b76487c)
# Penyelesaian Task 5
1. Edit file `/etc/fstab` agar filesystem otomatis mounted ketika system bootup
   - `nano /etc/fstab`
   - masukan line sesuai format : `LABEL=<your-label>  <mount-point>  <file-system-type>  <mount-option>  <dump>  <pass>`
   - ![image](https://github.com/diotriandika/learn-networking/assets/109568349/1b170016-681a-4340-828c-a3381c6f3b1a)

![image](https://github.com/diotriandika/learn-networking/assets/109568349/8dd0b962-1cb3-4d1a-82c9-ff50a3fb76c9)
# _*You Accomplished The Task!*_
