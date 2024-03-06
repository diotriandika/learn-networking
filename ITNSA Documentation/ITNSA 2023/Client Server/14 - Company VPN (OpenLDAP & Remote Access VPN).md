# Company VPN ( On Going )

**Tasks:**

---

- Install and configure LDAP with OpenLDAP in buton.lks.id
  - Use domain dc=lks,dc=id
  - Create OU VPN to store all VPN users
  - Create user remote with password Skills39 in the VPN OU to be used during VPN authentication

### Step 1 - Configure OpenLDAP

Install OpenLDAP

```bash
$ sudo apt install slapd ldap-utils
```

Reconfigure ldap

```bash
$ sudo reconfigure slapd
---
no
lks.id
LKS
no
yes
---
```

Add Group

```bash
$ sudo nano base.ldif
---
dn: ou=VPN,dc=lks,dc=id
objectClass: organizationalUnit
ou: VPN
---
```

query group

```bash
ldapadd -x -D "cn=admin,dc=lks,dc=id" -W -f base.ldif
```

check

```bash
$ slapcat
```

### Step 2 - Create user

---
- Install ldapscripts
```bash
$ sudo apt install ldapscripts
```
Edit File ldapscripts.conf
```bash
$ sudo nano /etc/ldapscripts.conf
---
#Uncomment pada SUFFIX, dan USUFFINX. lalu edit BINDDN
SUFFIX
USUFFIX
BINDDN
```
Rubah isi dari file ldapscripts.passwd
```bash
echo -n "Skills39s >> /etc/ldapscripts/ldapscripts.passwd
```
Lalu tambahkan user
```bash
ldapadduser vpn 1000
```
Rubah password user nya
```bash
ldapsetpasswd vpn Skills39s
```

