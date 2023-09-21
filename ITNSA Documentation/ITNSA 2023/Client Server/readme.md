# Nyoba LKS ITNSA 2023

Topologi : 

![image-20230921065702656](C:\Users\tpmst\AppData\Roaming\Typora\typora-user-images\image-20230921065702656.png)

## Malaka Network

> Note : Jika teks dalam format *italic* bisa diskip dulu karena belum pasti

### DNS Server

> Referensi dari soal LKS 2021

- Access aur.lks.id, cilik.lks.id *and muna.lks.id* then install bind9. Configure it to server domain lks.id using the private addresses.

- Create A Records of buton.lks.id, muna.lks.id and rote.lks.id that points to their respective addresses

- Create NS records:

  - ns1.lks.id that points to addresses of aur.lks.id
  - ns2.lks.id that points to addresses of cilik.lks.id
  - *ns3.lks.id that points to addresses of muna.lks.id*

- On both name server, create the following subdomains using address record:

  - *www.lks.id that points to all addresses of buton.lks.id*

  - cert.lks.id that points to all addresses of rote.lks.id

  - 25 user subdomain that points to all addresses buton.lks.id

    - user01.public.lks.id
    - user02.public.lks.id

    ​       . . .

    - user25.lks.id

### Web Server

> Referensi dari soal LKS 2021

- Access aur.lks.id and cilik.lks.id then install apache2. Configure it to serve all websites in Malaka Network.

  - Install curl or lynx to test the web service

- Create /var/www/index.html default page with content specified in Appendix.

- Create /var/www/userXX/index.html default page for 25 users with content specified in Appendix. Example :

  - /var/www/user01/index.html

  - /var/www/user02/index.html

    . . . 

  - /var/www/user25/index.html

- *Create virtual host www.lks.id serving /var/www/index.html*

- Create 25 virtual host userXX.public.lks.id serving /var/www/userXX/index.html. Example:

  - user01.public.lks.id serving /var/www/index.html

  - user02.public.lks.id serving /var/www/index.html

    . . .

  - user25.public.lks.id serving /var/www/index.html

- Create DNS Record for opsXX.lks.id and managers.lks.id

### IP Forwarding

> Belum ngerti tapi cobain dh pake soal LKS 2022
>
> Kemungkinan buton.lks.id allow IP Forwarding biar Malaka Network bisa saling bertukar informasi dengan Lombok Network

- Enable port NAT in NF to allow ??

### Site-to-site VPN with OpenVPN (botan to muna)

> Referensi dari soal LKS 2020

- Configure openvpn site-to-site tunneling to connect Malaka Network to Karimata Network
- Use UDP port 1945 for connection
- Use Certificate Authentication, create self-signed certificate as you wish / use certificate from rote.lks.id
- Make sure sipadan.lks.id able to access all resource on Malaka Network

### Iptables Traffic Logging

> Referensi dari soal LKS 2022
>
> masih kurang lengkap, coba search lagi

- Configure buton.lks.id using iptables LOG module, log following traffics:
  - Log incoming HTTPS traffic
  - Put all log in single file: /var/log/firewall.log



## Lombok Network

### Site-to-site VPN with OpenVPN (muna to botan)

> Referensi dari soal LKS 2020

- Configure openvpn site-to-site tunneling to connect Malaka Network to Karimata Network
- Use UDP port 1945 for connection
- *Use Certificate Authentication, create self-signed certificate as you wish / use certificate from rote.lks.id*
- Make sure sipadan.lks.id able to access all resource on Malaka Network

### Remote Access VPN with OpenVPN and LDAP Integration

> Referensi dari soal LKS 2022

- Configure LDAP in muna.lks.id to provide users available for VPN Authentication
  - Configure using domain dc=lks,dc=id
  - Create user 'vpn' with password 'Skills39' for VPN testing

- Configure OpenVPN in muna.lks.id  to provide remote access VPN to remote clients
  - Allow any client to connect using username and password authentication via LDAP
  - Configure remote clients to use following IP
    - Start: 10.20.22.10
    - End: 10.22.22.50
    - Subnet: 255.255.255.0
    - Gateway: 10.22.22.1
- *You can use aur.lks.id or cilik.lks.id to test the VPN Connection*

### File & Folder Sharing

#### Kemungkinan 1

> Referensi dari soal LKS 2021

- Create and Share folder C:\backup\ in host jukung.lks.id

  - Permit user Administrator only 

    > dalam kasus ini bisa coba pake Active Directory

- Create and Share folder C:\public\ in host jukung.lks.id

  - Disable authentication, anonymous user can read and write to this folder

#### Kemungkinan 2

> Referensi dari soal LKS 2022

- Configure muna.lks.id to host a CIFS shared folder that can be mounted at Windows Server
  - Use samba or any other similar application
  - Use Directory: /share
  - Allow all anonymous to read and write to the directory

### Linux iSCSI Target Server & Windows iSCSI Initator

> Referensi dari soal LKS 2021

- Configure iSCSI Initiator on komodo.lks.id
- Connect to all 5 disks in muna.lks.id iSCSI Target Server
- Create an NFTS filesystem and mount all 5 disks to E:/, F:/ and so on in any order
  - Make sure 5 extra disks is accessible via File Explorer

### Windows Backup

> Referensi dari soal LKS 2022 & 2021

- Create Backup Job using the windows server backup feature.

  - *Backup C:\ yang mau dibackup belum tau*

    > tapi coba buat backup nanti dari rote.lks.id, kemungkinan bisa jadi backup certificatenya atau web

  - Schedule the backup every day at 1 AM

### Cron/Scheduled Task

> Belum tau, tapi coba cari referensi tentang windows backup dengan cron job, atau sebagainya

### Sudo Restriction

> Referensi dari soal LKS 2022

- Install sudo in muna.lks.id and allow only user 'ops' to use sudo. Make sure all other users are not able to use sudo.
  - Create the user 'ops' with password 'Skills39'

### OpenLDAP (Sebagai Autentikasi)

> Referensi dari soal LKS 2022 & 2020

> Belum ngerti

### DNS Server

> Referensi dari soal LKS 2021

- Access aur.lks.id, cilik.lks.id *and muna.lks.id* then install bind9. Configure it to server domain lks.id using the private addresses.

- Create A Records of buton.lks.id, muna.lks.id and rote.lks.id that points to their respective addresses

- Create NS records:

  - ns1.lks.id that points to addresses of aur.lks.id
  - ns2.lks.id that points to addresses of cilik.lks.id
  - *ns3.lks.id that points to addresses of muna.lks.id*

- On both name server, create the following subdomains using address record:

  - *www.lks.id that points to all addresses of buton.lks.id*

  - cert.lks.id that points to all addresses of rote.lks.id

  - 25 user subdomain that points to all addresses buton.lks.id

    - user01.public.lks.id
    - user02.public.lks.id

    ​       . . .

    - user25.lks.id

- Create DNS Record for opsXX.lks.id and managers.lks.id

### Email Server with SMTP and IMAP

> Referensi dari soal LKS 2022

- Configure muna.lks.id as a centralized mail server using any application that supports SMTP and IMAP using negotiable TLS
  - Use the domain lks.id so mail can be sent directly to @lks.id mail address.
  - Configure SMTP to listen in port 25
    - Enable negotiable TLS using certificate from Corporate CA
  - Configure IMAP to listen in port 143
    - Enable negotiable TLS using certificate from Corportate CA
- Configure Mail Users according to  table in the appendix
- Configure Mail Groups notification@lks.id with following members
  - ops@lks.id
  - dev@lks.id

### Roundcube Webmail

> Referensi dari soal LKS 2022

- Enable web-based email using roundcube in muna.lks.id
  - Enable HTTPS access using certificate from Corporate CA
  - Make it accessible with the domain webmail.lks.id

### DFS Replication

> Bisa jadi dfs ini nanti fungsinya sebagai replication untuk folder atau file backupan, mungkin bisa dipakai sebagai failover untuk folder sharing di windows server.



## Karimata Network

### Web Server

> Referensi dari soal LKS 2022

- Configure rote.lks.id to hosts all these websites

  - managers.itnsa.id at C:\managers

  - dev.itnsa.id at C:\dev

  - 10 ops website:

    - ops01.lks.id at C:\web\ops01

    - ops02.lks.id at C:\web\ops02

    - ops03.lks.id at C:\web\ops03

      . . .

    - ops10.lks.id at C:\web\ops10

- Enable basic authentication for managers.lks.id, allow user 'manager' with password 'Skills39'

- Enable HTTPS for managers.lks.id and dev.lks.id

  - Use Corporate CA that points to wildcard domain of *.lks.id
  - Refer to the appendix for website content
  - Make sure the DNS record is also created at DNS Server

### DHCP Server

> Masih determine, sepertinya untuk dhcp ke client sipadan.lks.id, karena di Lombok Network tidak terdapat service DHCP Relay yang diperlukan.

### Raid

> Konfigurasi RAID, coba' aja konfigurasi RAID 1,5,6 dan 10

### Certificate Authority

> Referensi Soal LKS 2021 
>
> Masih rancu sama sistem penggunaan certificate untuk service

- Access rote.lks.id and Configure CA to issue required Certificates by Linux Services and other
  - Common Name : LKSN2023-CA
- Generate certificates required by other services with the following DNS name:
  - www.lks.id
  - *.public.lks.id
  - webmail.lks.id
  - managers.lks.id
  - dev.lks.id
  - Save these certificates required by other services with the following DNS name:
  - Use Skills39 as Export Password
  - *Use DNS name as filename:*
    - *www.lks.id.pfx*
    - *public.lks.id.pfx*
    - *webmail.lks.id.pfx*
    - *managers.lks.id.pfx*
    - *dev.lks.id.pfx*

### Webmail Client

> Mungkin ini sebagai pointer untuk memahami topologi, jadi nantinya sipadan.lks.id akan sebagai device yang akan menggunakan service webmail dkk



# Appendix



## IP Address Table

**Malaka Network**

| Hostname     | Operating System  | IP Address /25 | Interface |
| ------------ | ----------------- | -------------- | --------- |
| aur.lks.id   | Debian 11.xx DLBD | 45.8.17.21     | ens33     |
| buton.lks.id | Debian 11.xx DLBD | 45.8.17.126    | ens33     |
|              |                   | 10.196.10.126  | ens36     |
| cilik.lks.id | Debian 11.xx DLBD | 45.8.17.22     | ens33     |

**Lombok Network**

| Hostname       | Operating System            | IP Address /25 | Interface |
| -------------- | --------------------------- | -------------- | --------- |
| muna.lks.id    | Debian 11.xx DLBD           | 10.196.10.125  | ens33     |
|                |                             | 10.200.2.126   | ens36     |
| jukung.lks.id  | Windows Server 2019 desktop | 10.196.10.21   | eth1      |
| komodo.lks.id  | Windows Server 2019 desktop | 10.196.10.22   | eth1      |
| ligitan.lks.id | Windows 10                  | 10.195.10.10   | eth1      |

**Karimata Network**

| Hostname       | Operating System            | IP Address /25 | Interface |
| -------------- | --------------------------- | -------------- | --------- |
| rote.lks.id    | Windows Server 2019 desktop | 10.200.2.21    | eth1      |
| sipadan.lks.id | Debian 11.xx Desktop        | 10.200.2.10    | eth1      |



## Web Content

/var/www/index.html

```html
<h1> Default Page </h1><br>
<br>
This page has not been modified by the owners.
```

---

/var/www/userXX/index.html

```html
<h1> Default Page for userXXX </h1><br>
<br>
This page has not been modified by the user.
```

---

managers.lks.id

```html
<h1> managers.itnsa.id </h1>
This website is managed by admin@itnsa.id
```

---

dev.lks.id

```html
<h1> dev.itnsa.id </h1>
This website is managed by dev@itnsa.id
```

---

opsXX.lks.id

```html
<h1> opsXX.itnsa.id </h1>
This website is managed by ops@itnsa.id
```
