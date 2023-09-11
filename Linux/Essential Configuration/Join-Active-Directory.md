# Cara Join Active Directory Windows Server with Realmd
Pre-configured :
- IP Address 
- Hostname FQDN
Detailed Active Directory
```bash
Domain Server  : Windows Server 2019
Domain Name    : lks.id
Hostname       : komodo.lks.id
NetBIOS Name   : LKS
Realm          : lks.id
```
## Step 1
Install required packages
```bash
root@muna:~# apt install realmd sssd sssd-tools libnss-sss libpam-sss samba-common-bin oddjob oddjob-mkhomedir packagekit adcli
```
## Step 2
Config DNS
```bash
# Edit file resolv.conf
root@muna:~# nano /etc/resolv.conf

# resolv.conf configuration
domain lks.id
search lks.id
nameserver 45.8.17.21
```
Discover Domain Active Directory
```
root@muna:~# realm discover lks.id
lks.id
 type: kerberos
 realm-name: LKS.ID
 domain-name: lks.id
 configured: no
 ...
```
Join in Active Directory Domain
```bash
root@muna:~# realm join lks.id
Password for Administrator:   <-- # Enter Administrator Active Directory Password
```

Reference :
- https://www.server-world.info/en/note?os=Debian_11&p=realmd
