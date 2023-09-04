# Langkah - Langkah Instalasi dan Konfigurasi OpenLDAP Server
Apa itu LDAP Server? 

LDAP Server atau (Lightweight Directory Access Protocol) yaitu server yang biasanya digunakan untuk menyimpan dan mengelola informasi direktori seperti pengguna, organisasi atau grup secara terpusat. LDAP juga bisa digunakan sebagai sumber untuk autentikasi untuk server lainnnya, seperti Mail Server, VPN Server, File Server dll.

Untuk membuat LDAP disini saya menggunakan software Open-Source yang mengimplementasikan sistem dari LDAP yaitu OpenLDAP. OpenLDAP memberikan layanan direktori yang didasarkan pada model client-server
- Domain Name / DNS configured
## Instalasi OpenLDAP
Update Package dan Install OpenLDAP packages. `slapd` adalah package utama dari OpenLDAP dan `ldap-utils` menyediakan command-line untuk memanagemen OpenLDAP Server.
```
sudo apt install slapd ldap-utils
```
Akan muncul menu dibawah yang mana ini ditunjukan untuk setup password admin OpenLDAP. Masukan Password kemudian confirm lagi password dan select `Ok`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/16af989f-356c-49d4-a89c-584b5bfeddd5)
>
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/44a7c10c-a0c0-435d-a8f1-c02a2f485cc3)
## Konfigurasi OpenLDAP
Reconfigure slapd
```
sudo dpkg-reconfigure slapd
```
Akan muncul menu untuk menghapus konfigurasi OpenLDAP, Select `No` akan menyimpan konfigurasi yang telah dibuat sebelumnya. 
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/67ccf558-df3d-4f5d-a389-25524402930c)

Masukan domain untuk OpenLDAP server.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/f1f99d11-dcfb-45e8-932e-18b5679b4991)

Masukan `Organization Name`, opsional, kita bisa biarkan default dengan select `Ok`
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/8b2dd8da-88aa-4838-8af8-0f023007321f)

Masukan password untuk administrator dan confirm password
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/6b03e286-268e-403a-90e9-e1f2d79a4929)
>
>![image](https://github.com/diotriandika/learn-networking/assets/109568349/d58ab3f5-aff1-4445-b537-c7aae14d3e83)

Select `No` untuk tidak menghapus database lama
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/3e128196-c61b-4072-843e-27989069d74f)

Selanjutnya select `Yes` untuk memindahkan database lama.
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/db8808eb-1b7d-41ba-9807-56779834310d)

Untuk meverifikasi konfigurasi `slapd` gunakan command
```
sudo slapcat
```
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/cfc03ba5-2419-4c53-9cdc-683aec85a39a)

## Menambahkan User LDAP
Untuk menambahkan user ke OpenLDAP, kita amenggunakan file dengan format `.ldif` atau _LDAP Data Interexchange Format_

Buat file dengan format `<username>.ldif`
```
sudo nano <username>.ldif
```
Menabahkan entry baru dengan mengedit file `<username>.ldif`. Cek [disini](https://tylersguides.com/guides/openldap-how-to-add-a-user/) untuk melihat format yang bisa dibuat

Disini berhubung saya hanya ingin membuat user biasa untuk autentikasi email dan vpn saya akan buat dengan format dibawah:
```
dn: cn=lnearher,dc=itnsa,dc=id
objectClass: posixAccount
objectClass: inetOrgPerson
cn: lnearher
sn: lnearher
uid: lnearher
uidNumber: 1010
gidNumber: 101
userPassword: Skills39
loginShell: /bin/bash
homeDirectory: /home/lnearher
mail: lnearher@itnsa.id
```
> Keterangan :
> - `dn` yaitu distinguish name
> - `cn` yaitu common name
> - `dc` yaitu domain control

Kita juga bisa membuat batch user dengan menambahkan line baru dibawah file ldif sebelumnya, contoh :
> ![image](https://github.com/diotriandika/learn-networking/assets/109568349/2d9b2210-4562-4328-b6e3-f34c71467bbc)

Jika sudah tambahakan user dengan command `ldapadd`
```
sudo ldapadd -x -W -D "cn=admin,dc=itnsa,dc=id" -f lnearher.ldif
```
![image](https://github.com/diotriandika/learn-networking/assets/109568349/a9467b90-2d89-4714-899c-5fa60b891070)
> Keterangan
> - Nyusul masi nyari-nyari

## Install dan Konfigure LDAP Client
LDAP Client disini digunakan untuk akses LDAP Server yang sudah dibuat. Install `libnss-ldap` dan `libpam-ldap`
```
sudo apt install libnss-ldap
```
pokonya disable ncsd service


Referensi :
- https://tylersguides.com/guides/openldap-how-to-add-a-user/
- https://www.howtoforge.com/how-to-install-openldap-on-debian-11/
- https://www.thegeekstuff.com/2015/02/openldap-add-users-groups/
- https://www.digitalocean.com/community/tutorials/how-to-manage-and-use-ldap-servers-with-openldap-utilities
- https://www.thegeekstuff.com/2015/01/openldap-linux/
- https://www.digitalocean.com/community/tutorials/how-to-use-ldif-files-to-make-changes-to-an-openldap-system
- https://linuxhint.com/configure-ldap-client-debian/
