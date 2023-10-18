# Company VPN (OpenLDAP STUCK!!)

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

Create user

```bash
$ sudo nano user.ldif
---
dn: cn=vpn,ou=VPN,dc=lks,dc=id
objectClass: inetOrgPerson
objectClass:
```


