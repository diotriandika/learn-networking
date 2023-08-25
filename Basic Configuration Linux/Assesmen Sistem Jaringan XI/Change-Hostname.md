# Langkah - Langkah Mengganti Hostname
Hostname sebuah perangkat bisa dikatakan nama/alias dari sebuah perangkat tersebut.
## Mengganti Hostname melalui /etc/hostname
Edit file `/etc/hostname` kemudian rename hostname sebelumnya dengan hostname yang diinginkan

    sudo nano /etc/hostname

Edit file `/etc/hosts` dan rename default hostname dengan hostname yang baru kemudian save & exit

    sudo nano /etc/hosts

> Sebelum
> 
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/ef2ca9f0-6f87-464b-8677-5f003e91ae37)
>
> Sesudah
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/edc7b603-1d70-4ae4-b8b6-0f480fcda632)

## Mengganti Hostname menggunakan hostnamectl
Gunakan program `hostnamectl` untuk mengganti hostname

    sudo hostnamectl set-hostname <new-hostname>

Edit file `/etc/hosts` dan rename default hostname dengan hostname yang baru kemudian save & exit

    sudo nano /etc/hosts

## Verifikasi
Untuk melihat apakah hostname sudah berhasil diubah atau belum, bisa menggunakan command `hostname`, re-login atau juga bisa langsung reboot system
