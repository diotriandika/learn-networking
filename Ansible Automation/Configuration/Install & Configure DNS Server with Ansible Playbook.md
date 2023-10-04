# Install & Configure DNS Server with Ansible Playbook

Dalam ansible, kita bisa melakukan instalasi dan sekaligus konfigurasi pada DNS Server yang berada dalam cakupan Managed Host. 

**Task:**

- Create a playbook called 3-dns-server.yml for configuring at least two DNS Servers.
  - Install a DNS Service on all host in the group "dns"
  - The first host in "dns" group should be master for the domain "applix.com"
  - All other hosts in this group should be slave DNS Server for domain "applix.com"
  - Ensure every hosts listed in /etc/ansible/hosts has A record for <hostname>.applix.com
  - The domain intranet.applix.com must point to 10.22.0.51

**Prequisites:**

- Address Configured
- Repository Updated
- Python, SSH, Ansible Installed and for SSH already Configured.

**Appendix:** 

| Device ID | IP            | Description     |
| --------- | ------------- | --------------- |
| HOST      | 10.22.0.50/24 | Controller Node |
| LIN1      | 10.22.0.1/24  | Managed Node    |
| LIN2      | 10.22.0.2/24  | Managed Node    |
| LIN3      | 10.22.0.3/24  | Managed Node    |

## Install & Configuring DNS Server with Ansible Steps

### Step 1 (Basic Configuration)

#### HOST>

##### Inventory :

```yaml
all:
 children:
  LIN_Host:
   hosts:
    LIN1:
     ansible_host: 10.22.0.1
     ansible_user: root
     ansible_ssh_pass: Skills39
     hostname: LIN1
    LIN2:
     ansible_host: 10.22.0.2
     ansible_user: root
     ansible_ssh_pass: Skills39
     hostname: LIN2
    LIN3:
     ansible_host: 10.22.0.3
     ansible_user: root
     ansible_ssh_pass: Skills39
     hostname: LIN3
  dns:
   hosts:
    LIN1:
    LIN2:
    LIN3:
```

##### Check connectivity

```bash
$ sudo ansible -m ping all
```

### Step 2 (Create Playbook Task 1 - Installing BIND9 - Task 1.1)

#### HOST>

##### Create playbook named 3-dns-server.yml

```bash
# Move to /etc/ansible directory
$ cd /etc/ansible

# Create and Edit file 3-dns-server.yml
$ sudo nano 3-dns-server.yml
```

###### 3-dns-server.yml

```yaml
---
- name: Install DNS Server
  hosts: dns
  become: yes
  
  tasks:
  - name: Install BIND9
    apt:
     name: bind9
     state: present
  - name: Ensuring BIND9 Started
    service:
     name: bind9
     state: started
```

#### Verfikasi & Coba Run Playbook

```bash
## Verifikasi dulu ya
$ sudo ansible-playbook 3-dns-server.yml --check # --check < parameter to only simulate do the playbook without any changed. Biasanya akan error pada task "Ensuring BIND9 Started" karena bind9 disini hanya disimulasikan, tidak terinstall secara fisik jadinya missing deh

## Run Playbook
$ sudo ansible-playbook 3-dns-server.yml 
--output
PLAY [Install DNS Server] **********************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [LIN1]
ok: [LIN3]
ok: [LIN2]

TASK [Install BIND9] ***************************************************************************************************
changed: [LIN3]
changed: [LIN2]
changed: [LIN1]

TASK [Ensuring BIND9 Started] ******************************************************************************************
ok: [LIN3]
ok: [LIN1]
ok: [LIN2]

PLAY RECAP *************************************************************************************************************LIN1                       : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
LIN2                       : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
LIN3                       : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### Step 2 (Configure DNS Server Master Task 1.2)

#### HOST>

##### Buat direktori, agar mudah untuk navigasi

```bash
$ mkdir -p DNS-Config/Master
$ cd /etc/ansible/DNS-Config/Master
```

##### Install bind9 (hanya untuk mengambil template)

```bash
$ sudo apt-get install bind9
```

##### Copy file konfigurasi yang dibutuhkan ke workdir

```bash
$ sudo cp /etc/bind/named.conf.local /etc/ansible/DNS-Config/Master/
$ sudo cp /etc/bind/db.local /etc/ansible/DNS-Config/forward
$ sudo cp /etc/bind/db.127 /etc/ansible/DNS-Config/reverse
```

##### Edit file named.conf.local

```bash
$ sudo nano named.conf.local
---
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "applix.com"{
        type master;
        file "/etc/bind/forward";
        allow-transfer {10.22.0.2; 10.22.0.3; };
};

zone "0.22.10.in-addr.arpa" {
        type master;
        file "/etc/bind/reverse";
        allow-transfer {10.22.0.2; 10.22.0.3; };
};
---
```

##### Edit file forward (Create DNS Records)

```bash
$ sudo nano forward
---
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
LIN2    IN      A       10.22.0.2
LIN3    IN      A       10.22.0.3
intranet        IN      A       10.22.0.51
---
```

##### Edit file reverse (DNS Pointer)

```bash
$ sudo nano reverse
---
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
1       IN      PTR     LIN1.applix.com.
2       IN      PTR     LIN2.applix.com.
3       IN      PTR     LIN3.applix.com.
51      IN      PTR     intranet.applix.com.
---
```

##### Buat Task lagi untuk konfigurasi DNS Server Master pada playbook 3-dns-server.yml 

```bash
$ cd /etc/ansible
$ sudo nano 3-dns-server.yml
```

```yaml
- name: Install DNS
  hosts: dns
  become: yes
  tasks:
  - name: Install BIND9
    apt:
     name: bind9
     state: present
  - name: Ensuring BIND9 Started
    service:
     name: bind9
     state: started
     enabled: yes
    notify: Restart BIND9

# Tambahkan Line ini (Untuk Konfigurasi Master)
- name: Configure DNS Master
  hosts: LIN1
  become: yes
  tasks:
  - name: Copying DNS Configuration Files
    copy:
     src: /etc/ansible/DNS-Config/Master/named.conf.local
     dest: /etc/bind/named.conf.local
     owner: root
     group: root
     mode: 0644
  - name: Copying DNS Forward Zone
    copy:
     src: /etc/ansible/DNS-Config/forward
     dest: /etc/bind/forward
     owner: root
     group: root
     mode: 0644
  - name: Copying DNS Reverse Zone
    copy:
     src: /etc/ansible/DNS-Config/reverse
     dest: /etc/bind/reverse
     owner: root
     group: root
     mode: 0644
    notify: Restart BIND9 		# <--- ini buat ngetrigger handlernya
  handlers:
  - name: Restart BIND9
    service:
     name: bind9
     state: restarted
```

#### Verifikasi & Coba Run Playbook

```bash
## Verifikasi dulu ya
$ sudo ansible-playbook 3-dns-server.yml --check

## Run Playbook
$ sudo ansible-playbook 3-dns-server.yml
```



### Step 3 (Configure DNS Slave Task 1.3)

#### HOST>

##### Buat direktori agar mudah untuk navigasi

```bash
$ mkdir /etc/ansible/DNS-Config/Slave
$ cd /etc/ansible/DNS-Config/Slave
```

##### Copy file konfigurasi yang dibutuhkan ke workdir

```bash
$ sudo cp /etc/ansible/DNS-Config/Master/named.conf.local named.conf.local
```

> Disini saya hanya mengcopy konfigurasi file `named.conf.local` karena dalam mengkonfigurasi DNS Slave hanya memerlukan file `zone.conf.local`. 

##### Edit file `named.conf.local` dan rubah sesuai dengan format dibawah

```bash
$ sudo nano named.conf.local
---
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "applix.com"{
        type slave;
        file "/var/cache/bind/forward";
        masters {10.22.0.1; };
};

zone "0.22.10.in-addr.arpa" {
        type slave;
        file "/var/cache/bind/reverse";
        masters {10.22.0.1; };
};
---
```

##### Buat task lagi dibawah pada playbook untuk DNS Slave

```yaml
# Installing DNS Service on host
- name: Install DNS
  hosts: dns
  become: yes
  tasks:
  - name: Install BIND9
    apt:
     name: bind9
     state: present
  - name: Ensuring BIND9 Started
    service:
     name: bind9
     state: started
     enabled: yes
    notify: Restart BIND9

# DNS Master Configuration
- name: Configure DNS Master
  hosts: LIN1
  become: yes
  tasks:
  - name: Copying DNS Configuration Files
    copy:
     src: /etc/ansible/DNS-Config/Master/named.conf.local
     dest: /etc/bind/named.conf.local
     owner: root
     group: root
     mode: 0644
  - name: Copying DNS Forward Zone
    copy:
     src: /etc/ansible/DNS-Config/forward
     dest: /etc/bind/forward
     owner: root
     group: root
     mode: 0644
  - name: Copying DNS Reverse Zone
    copy:
     src: /etc/ansible/DNS-Config/reverse
     dest: /etc/bind/reverse
     owner: root
     group: root
     mode: 0644

# Tambahkan Line ini (DNS Slave Configuration)
- name: Configure DNS Slave
  hosts: LIN2
  become: yes
  tasks:
  - name: Copying DNS Configuration File
    copy:
     src: /etc/ansible/DNS-Config/Slave/named.conf.local
     dest: /etc/bind/named.conf.local
     owner: root
     group: root
     mode: 0644
    notify: Restart BIND9 	# <--- tak pindahin kesini, biar semua service udah selesai keinstall baru trigger restart bind

  handlers:
  - name: Restart BIND9
    service:
     name: bind9
     state: restarted

```

#### Verifikasi & Coba Run Playbook

```bash
## Verifikasi dulu ya
$ sudo ansible-playbook 3-dns-server.yml --check

## Run Playbook
$ sudo ansible-playbook 3-dns-server.yml
```

### Step 4 (Create Playbook Task 2 - Configure DNS Client)

#### HOST>

##### Create Playbook `4-dns-client.yml`

```bash
$ cd /etc/ansible/
$ sudo nano 4-dns-client.yml
```

###### 4-dns-client.yml

```yaml
# Configure DNS Configuration for Client
- name: Cofigure DNS Nameservers
  hosts: LIN_Host
  become: yes
  tasks:
  - name: Primary DNS Nameserver
    lineinfile:
     path: /etc/resolv.conf
     line: "nameserver 10.22.0.1"
  - name: Secondary DNS Nameserver
    lineinfile:
     path: /etc/resolv.conf
     line: "nameserver 10.22.0.2"
  - name: Set Domain Suffix
    lineinfile:
     path: /etc/resolv.conf
     line: "search applix.com"
```

#### Verifikasi & Coba Run Playbook

```bash
## Verifikasi dulu ya
$ sudo ansible-playbook 4-dns-client.yml --check

## Run Playbook
$ sudo ansible-playbook 4-dns-client.yml
---output

PLAY [Cofigure DNS Nameservers] ****************************************************************************************

TASK [Gathering Facts] *************************************************************************************************ok: [LIN3]
ok: [LIN2]
ok: [LIN1]

TASK [Primary DNS Nameserver] ******************************************************************************************changed: [LIN1]
ok: [LIN2]
changed: [LIN3]

TASK [Secondary DNS Nameserver] ****************************************************************************************
changed: [LIN2]
changed: [LIN1]
changed: [LIN3]

PLAY RECAP *************************************************************************************************************LIN1                       : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
LIN2                       : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
LIN3                       : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

> Karena sebelumnya saya mengecek DNS Server pada LIN2 dengan menmbahkan DNS Nameserver, maka dari itu hanya LIN2 yang tidak ada perubahan

**Referensi :**

- https://stackoverflow.com/questions/42462435/ansible-provisioning-error-using-a-ssh-password-instead-of-a-key-is-not-possibl (mungkin perlu kalau fix ssh)
- Dokumentasi Ansible lengkap cuy
- ChatGPT
