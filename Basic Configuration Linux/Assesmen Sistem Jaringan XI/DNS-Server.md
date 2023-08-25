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
ayam goreng
```

> Note : Jika masih bingung dengan penggunaan DNS Record, bisa cek link dibawah
>
> https://simpledns.plus/help/dns-record-types

Selanjutnya restart service bind9 dengan command

    sudo systemctl restart bind9

Untuk melihat apakah dns forwarding sudah bekerja, gunakan tool nslookup seperti contoh dibawah





