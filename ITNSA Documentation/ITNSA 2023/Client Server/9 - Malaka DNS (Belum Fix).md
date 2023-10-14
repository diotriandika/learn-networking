# Malaka DNS 

**Tasks:** 

> Configure buton.lks.id as DNS Server for all malaka.id records.
>
> - You can use any service/application
> - Add all records required for Malaka Website
> - Point mail.lks.id to buton.lks.id's address in Malaka Network
> - Do not respond to query for (sub)domains other than malaka.id

### Step 1 - Installing DNS

Install bind9 in buton.lks.id

```bash
$ sudo apt-get install bind9
```

move to bind9 directory

```bash
$ cd /etc/bind
```

Edit `named.conf.local`

```bash
$ sudo nano named.conf.local
---
# add line

zone "malaka.id" {
	type master;
	file "/etc/bind/malaka.forward";
};

zone "17.8.45.in-addr.arpa" {
	type master;
	file "/etc/bind/malaka.reverse";
};
---
```

edit forward zone

```bash
$ sudo nano malaka.forward
---

```

