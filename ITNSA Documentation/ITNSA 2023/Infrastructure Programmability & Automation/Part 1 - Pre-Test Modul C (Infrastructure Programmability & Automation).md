# Pembahasan Pre-Test Modul C (Infrastructure Programmability & Automation) Part 1
## General Configuration

**Step 1 - Create Directory**

```bash
# Move to Ansible Directory
debian@HOST:~$ cd /etc/ansible
# Create Directory
debian@HOST:/etc/ansible$ sudo mkdir linux
```

**Step 2 - Securing** **`.vault_pass`** **with `Skills39` vault-password**

```bash
debian@HOST:/etc/ansible$ ansible-vault encrypt .vault_pass
```

> By default, we can't run ansible playbook while the needed variable/files encrypted with ansible-vault. We can usee `--ask-vault-pass` by the end of command line while executing the playbook

## Hostname

**Step 1 - Create Playbook**

```bash
# move to linux directory
debian@HOST:/etc/ansible$ cd linux
# create playbook
debian@HOST:/etc/ansible/linux$ sudo nano 1-hostname.yml
```

***1-hostname.yml >***

```yaml
---
- name: Configure Hostname
  hosts: LIN
  gather_facts: false
  vars_files:
  - '/etc/ansible/.vault_pass'
  
  tasks:
  - name: Configuring Hostname
    hostname:
     name: "{{ hostname }}"
```

## nftables

Skip dulu

## DNS

### Configuring DNS Server -->>

### **Step 1 - Create Playbook** - DNS Server

```bash
debian@HOST:/etc/ansible/linux$ sudo nano 3-dns-server.yml
```

***3-dns-server.yml*** - Installing Bind9

```yaml
---
- name: Configuring DNS
  hosts: dns
  gather_facts: false
  become: yes
  vars:
  - '/etc/ansible/.vault_pass'
  
  tasks:
  - name: Removing Dependencies
    apt:
     name: bind9-libs
     state: absent
  - name: Installing Bind9
     apt: bind9
     state: present
```

### **Step 2 - Configuring DNS Master**

> Install BIND9 pada HOST, cuma ngambil template aja

```bash
# Create New Directory
debian@HOST:/etc/ansible/linux$ cd ..
debian@HOST:/etc/ansible$ sudo mkdir templates
debian@HOST:/etc/ansible$ cd templates/
# Copy DNS Configuration File
debian@HOST:/etc/ansible/templates$ sudo cp /etc/bind/named.conf.local named.conf-master
debian@HOST:/etc/ansible/templates$ sudo cp /etc/bind/db.local forward
debian@HOST:/etc/ansible/templates$ sudo cp /etc/bind/db.127 reverse
```

Edit All Configuration file

```bash
debian@HOST:/etc/ansible/templates$ sudo nano named.conf-master forward reverse
```

***named.conf-master>***

```bash
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "applix.com"{
        type master;
        file "/etc/bind/forward";
        allow-transfer {10.22.0.2; };
};

zone "0.22.10.in-addr.arpa"{
        type master;
        file "/etc/bind/reverse";
        allow-transfer {10.22.0.2; };
};
```

> disini saya menggunakan LIN2 sebagai Slave sesuai dengan list pada inventory saya

***forward>***

```bash
; 
; BIND data file for local loopback interface
; 
$TTL    604800
@       IN      SOA     applix.com. root.applix.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      applix.com.  
@       IN      A       10.22.0.1
@       IN      A       10.22.0.2
LIN1    IN      A       10.22.0.1
LIN2    IN      A       10.22.0.2
LIN3    IN      A       10.22.0.3
LIN4    IN      A       10.22.0.4
HOST    IN      A       10.22.0.50
intranet        IN      A       10.22.0.51
WIN     IN      A       10.22.0.101
DEV-LIN IN      A       10.22.0.251
DEV-WIN IN      A       10.22.0.250
```

***reverse>***

```bash
; 
; BIND reverse data file for local loopback interface
; 
$TTL    604800
@       IN      SOA     applix.com. root.applix.com. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      applix.com.  
1       IN      PTR     NS1.applix.com.
2       IN      PTR     NS2.applix.com.
3       IN      PTR     NS3.applix.com.
4       IN      PTR     NS4.applix.com.
50      IN      PTR     HOST.applix.com.
51      IN      PTR     intranet.applix.com.
101     IN      PTR     WIN.applix.com.
250     IN      PTR     DEV-WIN.applix.com.
251     IN      PTR     DEV-LIN.applix.com.
```

### **Step 3 - Configuring DNS Slave**

Copy `named.conf-master` to `named.conf-slave` as slave configuration

```bash
debian@HOST:/etc/ansible/templates$ sudo cp named.conf-master named.conf-slave
```

***named.conf-slave>***

```bash
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "applix.com"{
        type slave;
        file "/var/cache/bind/forward";	# <<-- edit here
        masters {10.22.0.1; };	# <-- edit here
};

zone "0.22.10.in-addr.arpa"{
        type slave;
        file "/var/cache/bind/reverse";	# <-- edit here
        masters {10.22.0.1; };	# <-- edit here
};
```

### **Step 4 - Configure Playbook** - DNS Server

Open playbook & Add Tasks

```bash
debian@HOST:/etc/ansible/templates$ nano ../linux/3-dns-server.yml
```

***3-dns-server.yml***

```yaml
---
- name: Configuring DNS
  hosts: dns
  gather_facts: false
  become: yes
  vars:
  - '/etc/ansible/.vault_pass'
  
  tasks:
  - name: Removing Dependencies
    apt:
     name: bind9-libs
     state: absent
  - name: Installing Bind9
     apt: bind9
     state: present
     
  - name: Configuring DNS Master
    hosts: LIN1
    become: yes
    vars_files: '/etc/ansible/.vault_pass'
    tasks:
    - name: Copying DNS File Configuration
      copy:
       src: /etc/ansible/templates/named.conf-master
       dest: /etc/bind/named.conf.local
        - name: Copying DNS File Configuration
    - name: Copying DNS Zone Forward
      copy:
       src: /etc/ansible/templates/forward
       dest: /etc/bind/forward
    - name: Copying DNS Zone Reverse
      copy:
       src: /etc/ansible/templates/reverse
       dest: /etc/bind/reverse
    - name: Restarting Service
      service:
       name: bind9
       state: restarted
- name: Configuring DNS Slave
  hosts: LIN2
  become: yes
  gather_facts: false
  vars_files: '/etc/ansible/.vault_pass'
  tasks:
  - name: Copying DNS File Configuration
    copy:
     src: /etc/ansible/templates/named.conf-slave
     dest: /etc/bind/named.conf.local
  - name: Restarting Service
    service:
     name: bind9
     state: restarting
```

**Run Playbook**

### Configuring DNS Client -->>

### Step 1 - Create Playbook - DNS Client

```bash
debian@HOST:/etc/ansible/linux$ sudo nano 4-dns-client.yml
```

***4-dns-client.yml***

```yaml
---
- name: Setup DNS Client
  hosts: LIN
  become: yes
  gather_facts: false
  vars_files: '/etc/ansible/.vault_pass'
  tasks:
  - name: Adding DNS
    lineinfile:
     path: /etc/resolv.conf
     line: |
      domain applix.com
      nameserver 10.22.0.1
      nameserver 10.22.0.2
```

**Run Playbook**

## Web

### Step 1 - Create Playbook

```bash
debian@HOST:/etc/ansible/linux$ sudo nano 5-web-server.yml
```

***5-web-server.yml***

```yaml
---
- name: Setup Web
  hosts: web
  gather_facts: false
  become: yes
  vars_files: '/etc/ansible/.vault_pass'
  tasks:
  - name: Installing Nginx
    apt:
     name: nginx
     state: present
```

### Step 2 - Create HTML Templates with Jinja2

**Create `index.html` template**

Default Web

```bash
debian@HOST:/etc/ansible/templates$ sudo nano index.j2
```

***index.j2>*** 

```jinja2
<!DOCTYPE html>
<html>
<head>
	<title>{{ hostname }} Web Server</title>
</head>
<body style="color: {{ webcolor }};">
	<h1>Hello from {{ hostname }}</h1>
</body>
</html>
```

Intranet Web

```bash
debian@HOST:/etc/ansible/templates$ sudo nano index-intranet.j2
```

***index-intranet.j2>***

```jinja2
<!DOCTYPE html>
<html>
<head>
	<title>Intranet Web Server</title>
</head>
<body style="color: {{ webcolor }};">
	<h1>Welcome to the Intranet of Applix</h1>
	<p1>This site was served by {{ hostname }}</p1>
</body>
</html>
```

**Create Site Configuration Template**

Default Site

```bash
debian@HOST:/etc/ansible/templates$ sudo nano default.j2
```

***default.j2>***

```jinja2
server {
	listen {{ webport }};
	server_name {{ hostname }}.applix.com;
	location / {
		root /var/www/html;
		index index.html;
	}
}
```

Intranet Site

```bash
debian@HOST:/etc/ansible/templates$ sudo nano default-intranet.j2
```

***default-intranet.j2>***

```jinja2
server {
	listen 8081;
	server_name intranet.applix.com;
	location / {
		root /var/www/intranet;
		index index.html;
	}
}
```

### Step 3 - Configure Playbook 

```bash
debian@HOST:/etc/ansible/linux$ sudo nano 5-web-server.yml
```

***5-web-server.yml***

```yaml
---
- name: Setup Web
  hosts: web
  gather_facts: false
  become: yes
  vars_files: '/etc/ansible/.vault_pass'
  tasks:
  - name: Installing Nginx
    apt:
     name: nginx
     state: present
  - name: Copying Default Site Configuration
    template:
     src: /etc/ansible/templates/default.j2
     dest: /etc/nginx/sites-enabled/default
  - name: Copying Default Index
    template:
     src: /etc/ansible/tempates/index.j2
     dest: /var/www/html/index.html
  - name: Copying Intranet Site Configuration
    template:
     src: /etc/ansible/templates/default-intranet.j2
     dest: /etc/nginx/sites-enabled/intranet
  - name: Create Directory for Intranet
    file:
     path: /var/www/intranet
     state: directory
  - name: Copying Intranet Index
    template:
     src: /etc/ansible/templates/index-intranet.j2
     dest: /var/www/intranet/index.html
    notify: restart nginx
- handlers:
  name: restart nginx
  service:
   name: nginx
   state: restarted
```
**Run Playbook**
