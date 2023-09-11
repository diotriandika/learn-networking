# Langkah - Langkah Mengganti Hostname
Hostname sebuah perangkat bisa dikatakan nama/alias dari sebuah perangkat tersebut.
## Mengganti Hostname melalui /etc/hostname
Masuk User ROOT

Edit file `/etc/hostname` kemudian rename hostname sebelumnya dengan hostname yang diinginkan

    nano /etc/hostname

Reboot system menggunakan

    systemctl reboot

Jika terjadi error seperti dibawah

![image](https://github.com/diotriandika/learn-networking/assets/109568349/c97ad4b9-a161-4df0-980f-90b2b891b273)

Edit file `/etc/hosts` dan rename default hostname dengan hostname yang baru atau tambahkan line baru dibawah kemudian save & exit

    nano /etc/hosts

> Sebelum
> 
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/4490a06c-50ca-40a0-a796-f41af54ba02f)
>
> Sesudah
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/5b3a4d2f-7aa8-494c-bf2e-8079194dc445)
>
> Atau
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/a4fcac3f-5f11-4911-93ac-9cf715c52083)
 
## Mengganti Hostname menggunakan hostnamectl
Gunakan program `hostnamectl` untuk mengganti hostname

    sudo hostnamectl set-hostname <new-hostname>

Edit file `/etc/hosts` dan tambahkan `127.0.0.1 <hostname>` dibawah default hostname

    sudo nano /etc/hosts

## Verifikasi
Untuk melihat apakah hostname sudah berhasil diubah atau belum, bisa menggunakan command `hostname`, re-login atau juga bisa langsung reboot system
