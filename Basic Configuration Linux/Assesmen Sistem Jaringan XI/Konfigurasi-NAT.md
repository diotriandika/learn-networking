# Langkah - Langkah Konfigurasi NAT 
NAT atau Network Adresses Translation merupakan sebuah protokol yang berfungsi utuk mengubah atau memetakan IP Lokal menjadi IP Publik begitu juga sebaliknya. Gampangnya NAT itu buat memberikan akses keluar masuk IP Lokal dengan IP Publik.
## Konfigurasi NAT dengan iptables (temporary)
> Note : sebelum itu enable ipv4 forwarding
>
> Cek [disini](https://github.com/diotriandika/learn-networking/blob/99911b8455ac5cc6a0edf7eb9e1b885fa326437d/Basic%20Configuration%20Linux/Assesmen%20Sistem%20Jaringan%20XI/IP-Forwarding.md)

Gunakan format command dibawah

    sudo iptables -t nat -A POSTROUTING -o <eth-public> -j MASQUERADE

Disini IP Lokal seharusnya sudah terhubung dengan IP Publik



Namun konfigurasi ini akan hilang jika server direstart/reboot. Kita bisa menyimpan konfigurasi dengan cara berikut

## Permanent Rule NAT
Simpan ruleset iptables dengan command

    sudo iptables-save > /etc/iptables.up.rule

Selanjutnya buat executetable bash script untuk auto exec ketika booting

    sudo nano /etc/network/if-pre-up.d/iptables

Isi dengan script dibawah
```
#!/bin/sh
/sbin/iptables-restore < /etc/iptables.up.rules
```

Kemudian set permission file agar dapat di exec

    chmod +x /etc/network/if-pre-up.d/iptables

**_Done_**

Sebenarnya ada cara lebih mudah untuk menyimpan rules iptables, yaitu dengan `iptables presistent`

lebih lengkap bisa cek dokumentasi [disini](https://wiki.debian.org/iptables)
