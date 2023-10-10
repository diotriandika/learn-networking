# Pembahasan Pre-Test Modul C (Infrastructure Programmability & Automation) Part 1

## General Configuration

### **Step 1 - Create Directory**

```bash
# Move to Ansible Directory
debian@HOST:~$ cd /etc/ansible
# Create Directory
debian@HOST:/etc/ansible$ sudo mkdir linux
```

### **Step 2 - Securing** Vault-Pass

`.vault_pass` with `Skills39` vault-password

```bash
debian@HOST:/etc/ansible$ ansible-vault encrypt .vault_pass
```

By default, we can't run ansible playbook while the needed variable/files encrypted with ansible-vault. We can use `--ask-vault-pass` by the end of command line while executing the playbook

But here I'll using `pathing` for auto decrypting the `.vault_pass`

```bash
debian@HOST:/etc/ansible$ sudo nano .pass_vault 
```

> `.` is preventing the file shows while using list command. So the file keep hidden 

**.pass_vault>**

```plaintext
Skills39 
```

Add path of the file in `ansible.cfg`

```bash
debian@HOST:/etc/ansible$ sudo nano ansible.cfg
```

**ansible.cfg>**

```ini
[defaults]
vault_password_file = /etc/ansible/.pass_vault
```

## Hostname

**Step 1 - Create Playbook**

```bash
# move to linux directory
debian@HOST:/etc/ansible$ cd linux
# create playbook
debian@HOST:/etc/ansible/linux$ sudo nano 1-hostname.yml
```

**1-hostname.yml >**

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

> eksekusi nftables terakhir, karena kita tidak tahu service apa saja yang perlu kita beri

### Step 1 - Create Playbook

```bash
debian@HOST:/etc/ansible/linux$ sudo nano 2-nftables.yml
```

**2-nftables.yml>**

```yaml
---
- name: Filtering Traffic
  hosts: LIN
  become: yes
  gather_facts: false
  vars_files: '/etc/ansible/.vault_pass'
  tasks:
  - name: Copying nftables configuration
    copy:
     src: /etc/ansible/templates/nftables.conf
     dest: /etc/nftables.conf
  - name: Restarting Service
    service:
     name: nftables
     state: restarted
```

### Step 2 - Create Configuration File

```bash
debian@HOST:/etc/ansible/templates$ sudo nano nftables.conf
```

**nftables.conf>**

```jinja2
#/usr/sbin/nft -f

table inet LIN {
	chain input {
		type filter hook input priority 0; policy drop;
		ct state { established, related } accept
		tcp dport { 80, 8080, 9090, 8081 } accept
		ip saddr { 10.22.0.50, 10.22.0.251, 10.22.0.252 } ip protocol tcp tcp dport ssh accept
		ip saddr { 10.22.0.50, 10.22.0.251, 10.22.0.252 } ip protocol icmp accept
	}

}
```

**Run Playbook** 

## DNS

### Configuring DNS Server -->>

### **Step 1 - Create Playbook** - DNS Server

```bash
debian@HOST:/etc/ansible/linux$ sudo nano 3-dns-server.yml
```

**3-dns-server.yml** - Installing Bind9

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

**named.conf-master>**

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

**forward>**

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

**reverse>**

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

**named.conf-slave>**

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

**3-dns-server.yml**

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

**4-dns-client.yml**

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

**5-web-server.yml**

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

**index.j2>** 

```jinja2
<!DOCTYPE html>
<html>
<head>
	<title>{{ inventory_hostname }} Web Server</title>
</head>
<body style="color: {{ webcolor }};">
	<h1>Hello from {{ inventory_hostname }}</h1>
</body>
</html>
```

Intranet Web

```bash
debian@HOST:/etc/ansible/templates$ sudo nano index-intranet.j2
```

**index-intranet.j2>**

```jinja2
<!DOCTYPE html>
<html>
<head>
	<title>Intranet Web Server</title>
</head>
<body style="color: {{ webcolor }};">
	<h1>Welcome to the Intranet of Applix</h1>
	<p1>This site was served by {{ inventory_hostname }}</p1>
</body>
</html>
```

**Create Site Configuration Template**

Default Site

```bash
debian@HOST:/etc/ansible/templates$ sudo nano web-default.j2
```

**default.j2>**

```jinja2
server {
	listen {{ webport }};
	server_name {{ inventory_hostname }}.applix.com;
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

**default-intranet.j2>**

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

**5-web-server.yml**

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
     src: /etc/ansible/templates/web-default.j2
     dest: /etc/nginx/sites-enabled/web-default
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

## High Availability Intranet

### Step 1 - Create Playbook

```bash
debian@HOST:/etc/ansible/linux$ sudo nano 6-ha-intranet.yml
```

**6-ha-intranet.yml>**

```yaml
---
- name: High Availability
  hosts: ha
  become: yes
  gather_facts: false
  vars_files: '/etc/ansible/.vault_pass'
  
  tasks:
  - name: Installing HAProxy & KeepAlived
    apt: 
     name: 
     - haproxy
     - keepalived
     state: present
```

**Run playbook**

### Step 2 - Create HAProxy & KeepAlived Configuration Template with Jinja2

Keepalived

```bash
debian@HOST:/etc/ansible/templates$ sudo nano keepalived.conf.j2
```

**keepalived.conf.j2>**

```jinja2
vrrp_instance vrrp1 {
	state MASTER
	interface ens33
	virtual_router_id 50
	priority {{ priority }}
	advert_int 1
	authentication {
		auth_type PASS
		auth_pass P@ssw0rd
	}
	virtual_ipaddress {
		10.22.0.51
	}
}
```

HAProxy

```bash
debian@HOST:/etc/ansible/templates$ sudo nano haproxy.cfg.j2
```

**haproxy.cfg.j2**

```jinja2
defaults
	mode http
	balance roundrobin
	timeout client 30s

frontend intranet_front
	bind *:80
	option forwardfor
	default_backend intranet_back
	http-request add-header x-haproxy-host %[req.hdr(Host)]
backend intranet_back
	server LIN3 10.22.0.3:8081 check
	server LIN4 10.22.0.4:8081 check
```

### Step 3 - Adding New Variable

**Inventory:**

```ini
LIN]
LIN1 ansible_host=10.22.0.1 hostname=LIN1
LIN2 ansible_host=10.22.0.2 hostname=LIN2
LIN3 ansible_host=10.22.0.3 hostname=LIN3
LIN4 ansible_host=10.22.0.4 hostname=LIN4
DEV-LIN ansible_host=10.22.0.251 hostname=DEV-LIN
[dns]
LIN1
LIN2
[web]
LIN3 webport=8080 webcolor=green
LIN4 webport=9090 webcolor=red
[ha]
LIN3 priority=101	# <-- add new priority variable
LIN4 priority=100	# <-- add new priority variable

[WINDOWS]
WIN ansible_host=10.22.0.101 hostname=WIN
DEV-WIN ansible_host=10.22.0.150 hostname=DEV-WIN
[WINDOWS:vars]
ansible_port=5986
ansible_connection=winrm
ansible_winrm_transport=basic
ansible_winrm_server_cert_validation=ignore
[dc]
WIN

```

### Step 4 - Configure Playbook

```bash
debian@HOST:/etc/ansible/linux$ sudo nano 6-ha-intranet.yml
```

**6-ha-intranet.yml>**

```yaml
---
- name: High Availability
  hosts: ha
  become: yes
  gather_facts: false
  vars_files: '/etc/ansible/.vault_pass'
  
  tasks:
  - name: Installing HAProxy & KeepAlived
    apt: 
     name: 
     - haproxy
     - keepalived
     state: present
  - name: Copying HAproxy Configuration File
    template:
     src: /etc/ansible/templates/haproxy.cfg.j2
     dest: /etc/haproxy/haproxy.cfg
  - name: Copying KeepAlived Configuration File
    template: 
     src: /etc/ansible/templates/keepalived.conf.j2
     dest: /etc/keepalived/keepalived.conf
    notify: Restarting Service
  
  handlers:
  - name: Restarting Service
    service:
     name: keepalived
     state: restarted
  - name: Restarting Service
    service:
     name: haproxy
     state: restarted
```

**Run Playbook**

## Users

### Step 1 - Create Playbook

```bash
debian@HOST:/etc/ansible/linux$ sudo nano 7-users.yml
```

**7-users.yml>**

```yaml
---
- name: Add User to LIN
  hosts: LIN
  gather_facts: false
  become: yes
  vars_files: '/etc/ansible/.vault_pass'
  tasks:
  - name: Reading users.csv
    read_csv:
     path: /etc/ansible/users.csv
    register: user_list
    delegate_to: localhost
  - name: Printing user_list
    debug:
     var: user_list.list
  - name: Extracting user_list
    debug:
     msg: "{{ item.Username }}"
    loop: "{{ user_list.list }}"
  - name: Creating User
    user:
     name: "{{ item.Username }}""
     state: present
    loop: "{{ user_list.list }}"
```

**users.csv>**

```plaintext
Username,UID,First_name,Last_name,Groups,Password
booker12,9012,Rachel,Booker,Operations,iambooker
grey07,2070,Laura,Grey,Developers,iamgrey
johnson81,4081,Craig,Johnson,Operations,iamjohnson
jenkins46,9346,Mary,Jenkins,Developers,iamjenkins
smith79,5079,Jamie,Smith,Operations,iamsmith
```

**Run Playbook**
