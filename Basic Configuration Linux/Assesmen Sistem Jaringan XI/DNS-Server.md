# Langkah - Langkah Instalasi dan Konfigurasi DNS Server
DNS Server merupakan sebuah program yang berfungsi sebagai penerjemah sebuah Domain menjadi IP Address atau sebaliknya.
## Instalasi DNS Server
Install program DNS Server BIND9

    sudo apt-get install bind9 dnsutils -y

Masuk ke direktori `/etc/bind`

    cd /etc/bind

## Konfigurasi Zone Forward
> Zone Forward berfungsi sebagai penerjemah dari Domain ke IP Address

Edit file `named.conf.local`

    sudo nano /etc/named.conf.local

Tambahkan Line sebagai berikut kemudian save
```
zone "<domain-name.net>"{
    type master;
    file "/etc/bind/db.<your-db-file>";
};
```

Untuk membuat file `db.` sebagai forwarding, kita akan menyalin file default `db.local` menjadi file `db.<your-db-file`

    sudo cp db.local db.<your-db-file>

Selanjutnya edit file `db.<your-db-file>` 

    sudo nano db.<your-db-file>

Konfigurasi file sesuai dengan yang dibutuhkan, replace `localhost` dengan `<domain-name>`
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     <domain-name.net>. root.lnearher.id. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      lnearher.id.
@       IN      A       <address>
<www/dkk>    IN      A       <address>

```

> Note :

         - lnearher.id replace dengan domain kalian
         
         - <address> replace dengan IP yang ingin kalian forward (sesuaikan dengan yang ada di `named.conf.local`
         
         - <www/dkk> merupakan pembuka dari domain, gunakan sesuai kebutuhan.
> 
> Jika masih bingung dengan penggunaan DNS Record, bisa cek link [ini](https://simpledns.plus/help/dns-record-types)

Selanjutnya restart service bind9 dengan command

    sudo systemctl restart bind9

Untuk melihat apakah dns forwarding sudah bekerja, gunakan tool nslookup seperti contoh dibawah

![image](https://github.com/diotriandika/learn-networking/assets/109568349/b4e7cb8d-d069-4c79-8491-d9baa3f00b6e)

> Pastikan untuk mengatur nameserver/dns di client.

Disini Zone Forwarding sudah berhasil, namun jika kita coba menggunakan IP akan terdapat output seperti dibawah

![image](https://github.com/diotriandika/learn-networking/assets/109568349/75ae1780-6aa8-44e9-b32f-2a047982d684)

Disini tidak IP tidak da







