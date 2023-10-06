# Installing & Configure Failover & Loadbalacing Web with KeepAlived & HAproxy



**Tasks:**

- Create a playbook called 6-ha-intranet.yml for configuring two or more HA servers
- Install keepalibed and HAProxy on all hosts in the group "ha"
  - Configure 10.22.0.51 as floating IP and use the last host in "ha" group as VRRP master
  - If the master fails, the second last server in the "ha" group should take over and so on (priority in reverse order)
  - Use password authentication
  - Configure HAProxy to load balance "http://intranet.applix.com" between all available servers using roundrobin
    - Add Header "x-haproxy-host" with the hostname of current HAproxy host.

**Prequisites**:

- IP Address Configured
- Repository Updated
- Python & SSH Installed on all nodes
- Web Server Installed - check [here](https://github.com/diotriandika/learn-networking/blob/main/Ansible%20Automation/Configuration/Installing%20%26%20Configuring%20Web%20Server%20with%20Ansible%20Playbook.md)

**Appendix:**

| Device ID | IP            | Description     |
| --------- | ------------- | --------------- |
| HOST      | 10.22.0.50/24 | Controller Node |
| LIN1      | 10.22.0.1/24  | Managed Node    |
| LIN2      | 10.22.0.2/24  | Managed Node    |
| LIN3      | 10.22.0.3/24  | Managed Node    |

## Installing & Configure Failover & Loadbalancing Web with KeepAlived & HAProxy Steps

### Step 1 (Basic Configuration)

**DO IN HOST**

##### Inventory:

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
     webport: 9090  		
     webcolor: red 	
     priority: 101	# <-- tambahkan variabel baru, untuk set priority keepalived
    LIN3:
     ansible_host: 10.22.0.3
     ansible_user: root
     ansible_ssh_pass: Skills39
     hostname: LIN3
     webport: 8080		
     webcolor: blue
     priority: 100	# <-- tambahkan variabel baru, untuk set priority keepalived
  web:		
   hosts:
    LIN2:
    LIN3:
  ha:
   hosts:
    LIN2:
    LIN3:
```

#### Check Connectivity

```bash
$ sudo ansible -m ping all
```

### Step 2 (Create Playbook - Installing KeepAlived & HAProxy)

```bash
$ cd /etc/ansible
$ sudo nano 6-ha-intranet.yml
```

##### 6-ha-intranet.yml

```yaml
- name: Install & Configure Loadbalacing Failover
  hosts: ha
  become: yes
  vars:
   item:
   - keepalived
   - haproxy
  tasks:
  # Install package
  - name: Installing HAProxy & KeepAlived
    apt:
     name: "{{ item }}"
     state: present
```

#### Verifikasi & Coba Run Playbook

```bash
$ sudo ansible-playbook 6-ha-intranet.yml --check

$ sudo ansible-playbook 6-ha-intranet.yml
```

### Step 2 (Configure KeepAlived)

#### Buat file konfigurasi keepalived dengan Jinja2

```bash
$ cd templates
$ nano keepalived-conf.j2
```

##### keepalived-conf.j2

```jinja2
global defs {
	router_id {{ inventory_hostname }}
}

vrrp_instance VRRP {
	state MASTER
	interface ens33
	virtual_router_id 101
	priority {{ priority }}
	advert_int 1
	authentication {
		auth_type PASS
		auth_pass Skills39
	}
	virtual_ipaddress {
	10.22.0.51
	}
}
```

#### Configure 6-ha-intranet.yml

```bash
$ cd ..
$ sudo nano 6-ha-intranet.yml
```

##### 5-ha-intranet.yml

```yaml
- name: Install & Configure Loadbalacing Failover
  hosts: ha
  become: yes
  vars:
   item:
   - keepalived
   - haproxy
  tasks:
  # Install Package
  - name: Installing HAProxy & KeepAlived
    apt:
     name: "{{ item }}"
     state: present
  # Keepalived Configuration
  - name: Configuring Keepalived
    template:
     src: /etc/ansible/templates/keepalived-conf.j2
     dest: /etc/keepalived/keepalived.conf
    notify: Restart Service
   
  handlers:
  - name: Restart Service
    service: 
     name: keepalived
     state: restarted
```

#### Verifikasi & Coba Run Playbook

```bash
$ sudo ansible-playbook 6-ha-intranet.yml --check

$ sudo ansible-playbook 6-ha-intranet.yml
```

### Step 3 (Configure HAProxy)

#### Buat file konfigurasi haproxy dengan Jinja2

```bash
$ cd templates
$ sudo nano haproxy-conf.j2
```

##### haproxy-conf.j2

```yaml
defaults
	mode http
	log global
	balance roundrobin
	timeout client 30s

frontend intranet_front
	bind *:80
	option forwardfor
	default_backend intranet_back
	http-request add-header x-haproxy-host %[req.hdr(Host)]

backend intranet_back
	server LIN2 10.22.0.2:8081 check
	server LIN3 10.22.0.3:8081 check 
```

#### Configure 6-ha-intranet.yml

```bash
$ cd ..
$ sudo nano 6-ha-intranet.yml
```

##### 6-ha-intranet.yml

```yaml
- name: Install & Configure Loadbalacing Failover
  hosts: ha
  become: yes
  vars:
   item:
   - keepalived
   - haproxy
  tasks:
  # Install Package
  - name: Installing HAProxy & KeepAlived
    apt:
     name: "{{ item }}"
     state: present
  # Keepalived Configuration
  - name: Configuring Keepalived
    template:
     src: /etc/ansible/templates/keepalived-conf.j2
     dest: /etc/keepalived/keepalived.conf
  # HAProxy Configuration
  - name: Configuring HAProxy
    templates:
     src: /etc/ansible/templates/haproxy-conf.yml
     dest: /etc/haproxy/haproxy.cfg
    notify: Restart Service
  
  handlers:
  - name: Restart Service
    service:
     name: keepalived
     state: restarted
  - name: Restart Service
    service:
     name: haproxy
     state: restarted
```

#### Verifikasi & Coba Run Playbook

```bash
$ sudo ansible-playbook 6-ha-intranet.yml --check

$ sudo ansible-playbook 6-ha-intranet.yml
```

### Step 4 (Verifikasi)

Coba untuk melakukan ping ke floating ip dan juga curl / browse web intranet.applix.com

> masih bermasalah sama headernya, ga mau muncul dia bajingan

Referensi : 
- https://www.server-world.info/en/note?os=Debian_12&p=keepalived&f=1
- https://tecadmin.net/setup-ip-failover-on-ubuntu-with-keepalived/
- https://dev.to/vumdao/how-to-set-http-request-header-in-haproxy-48bd
- https://upcloud.com/resources/tutorials/haproxy-load-balancer-debian

