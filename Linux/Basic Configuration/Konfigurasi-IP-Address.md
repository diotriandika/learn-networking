# Langkah - Langkah Konfigurasi IP Address di Linux (Debian/Ubuntu)
Berikut adalah langkah langkah Konfigurasi IPv4 Di Debian 11
# Set Static IP Address
Cek IP Address dan Interface dari Host dengan mengetikan `ip a` pada command line. Maka akan keluar Output sebagai berikut

![image](https://github.com/diotriandika/learn-networking/assets/109568349/25b00b02-498f-4954-8734-86ebd66a593b)

Disini terlihat bahwa semua interface yang ada (diindikasikan dengan `ens`) tidak memiliki IP Address

Untuk menambahkan IP Address secara static, edit file `interfaces`

    sudo nano /etc/network/interfaces

![image](https://github.com/diotriandika/learn-networking/assets/109568349/ea2e67e9-1a6d-4786-a112-5485d89dcf5a)

Disini file konfigurasi interface masih kosong, kalian bisa menambahkan line dibawahnya sesuai dengan format dibawah
```
auto <interface>
iface <interface> inet static
    address <ip-address>
    netmask <subnetmask>
    gateway <ip-gateway>
    dns-nameserver <dns>
```
> Note :  - <interface> replace dengan nama dari interface yang ingin kalian tambahkan IP Address
>         - <ip-address> replace dengan IP Address yang ingin kalian tambahkan
>         - <subnetmask> replace dengan subnetmask dari IP Address, misal subnet dari prefix `/28` yaitu `255.255.255.240`
>         - gateway, ini opsional sesuaikan dengan Topologi
>         - dns-nameserver isi dengan DNS yang ingin ditambahkan, namun jika tidak bisa hilangkan.
>
> Contoh :
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/5095872e-fd23-4398-a1cb-54d399f0b44e)

**Exit dengan menggunakan CTRL+X kemudian konfirmasi dengan Y dan ENTER**

Selanjutnya untuk mengaplikasikan konfigurasi, restart service `networking` dengan menjalankan command berikut pada command line
```
sudo systemctl restart networking.service
```
atau
```
sudo /etc/init.d/networking restart
```
## Set DHCP IP Address
Sama seperti sebelumnya, masuk ke konfigurasi file `interfaces` lalu tambahkan line dibawah
```
auto <interface>
iface <interface> inet dhcp
```
> Contoh :
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/4e9d4449-b7d4-4879-a105-fa6a5263e9c2)

Restart Networking Service
```
sudo systemctl restart networking.service
```
atau
```
sudo /etc/init.d/networking restart
```

## Verifikasi
Cek apakah konfigurasi IP sudah benar apa belum dengan mengetikan command `ip a` pada command line

![image](https://github.com/diotriandika/learn-networking/assets/109568349/b10b8490-744a-4436-abe6-01eb450d0764)

