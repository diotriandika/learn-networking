# Installing & Configuring Web with Ansible

Dalam ansible, kita bisa melakukan instalasi dan sekaligus mengkonfigurasi Web Service yang berada dalam cakupan Managed Host.

**Task:**

- Create a playbook called 5-web-server.yml for configuring two or more web servers

- Install a web service on all hosts in the group "web"

  - The local website should listen on port "webport" variable in /etc/ansible/hosts file

  - Display the following content with textcolor based on the "webcolor" variable in /etc/ansible/hosts file

    ```plaintext
    "Hello from <hostname>!"
    ```

- Create a virtual host listening on port 8081 ccalled "intranet.applix.com" displaying the following content

  ```plaintext
  "Welcome to the intranet of Applix"
  "This site was served by <hostname>"
  ```

**Prequisites:**

- IP Address Configured
- Repository Updated
- Python & SSH Installed on all nodes

**Appendix:** 

| Device ID | IP            | Description     |
| --------- | ------------- | --------------- |
| HOST      | 10.22.0.50/24 | Controller Node |
| LIN1      | 10.22.0.1/24  | Managed Node    |
| LIN2      | 10.22.0.2/24  | Managed Node    |
| LIN3      | 10.22.0.3/24  | Managed Node    |

## Installing & Configuring Web Server in Ansible Steps

#### do on controller node!!

### Step 1 (Basic Configuration)

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
     webport: 9090  		# <-- add this variable to determine port
     webcolor: red 		# <-- add this variable to set web color
    LIN3:
     ansible_host: 10.22.0.3
     ansible_user: root
     ansible_ssh_pass: Skills39
     hostname: LIN3
     webport: 8080		# <-- add this variable to determine port
     webcolor: blue 	# <-- add this variable to set web color
  web:		# <-- this is the group of web host
   hosts:
    LIN2:
    LIN3:
```

##### Check Connectivity

```bash
$ sudo ansible -m ping all
```

### Step 2 (Create HTML Web Base with Jinja2 Template - Task 1 & 2)

#### Create HTML index with Jinja2

```bash
# Create Directory for placing the Jinja2 index.j2
$ cd /etc/ansible
$ sudo mkdir templates
$ cd templates/

# create index.j2 file
$ sudo nano index.j2
```

##### index.j2:

```jinja2
<!DOCTYPE html>
<html>
<head>
	<title>Web Server</title>
</head>
<body style="color: {{ webcolor }};"
	<h1>Hello from {{ inventory_hostname }}!</h1>
</body>
</html>
```

#### Create Sites Configuration for HTML with Jinja2

##### default.j2

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

### Step 3 (Create Playbook Task - 1 & 2)

#### Create Playbook named 5-web-server.yml

```bash
$ sudo nano 5-web-server.yml
```

##### 5-web-server.yml

```yaml
- name: Install & Configure Web Server
  hosts: web
  become: yes
  
# Create default web
  tasks:
  - name: Install nginx
    apt:
     name: nginx
     state: present
  - name: Copy HTML index file
    template:
     src: /etc/ansible/templates/index.j2
     dest: /var/www/html/index.html
  - name: Copying HTML Sites Configuration
    template:
     src: /etc/ansible/templates/default.j2
     dest: /etc/nginx/sites-available/default
  - name: Copying HTML index
    template:
     src: /etc/ansible/templates/nginx.j2
     dest: /etc/nginx/sites-available/default
    notify: Restarting Webserver

  handlers:
  - name: Restarting Webserver
    service:
     name: nginx
     state: restarted
```

#### Verifikasi & Coba Run Playbook

```bash
$ sudo ansible-playbook 5-web-server.yml
```

Coba untuk cek web. Buka browser dan masukan IP address serta specific port

```plaintext
http://10.22.0.2:9090
http://10.22.0.3:8080
```

### Step 4 (Create Intranet Web with Jinja2 Template - Task 3)

#### Create HTML index with Jinja2

```bash
$ sudo nano intranet-index.j2
```

##### intranet-index.j2

```jinja2
<!DOCTYPE html>
<html>
<head>
	<title>Intranet Web</title>
</head>
<body>
	<h1>Welcome to the Intranet of Appliix</h1>
	<p1>This site was served by {{ inventory_hostname }}</p1>
</body>
</html>
```

#### Create HTML Site Configuration

```bash
$ sudo nano intranet-site.j2
```

##### intranet-site.j2

```jinja2
server {
	listen 8081;
	server_name intranet.applix.com
	
	location / {
		root /var/www/intranet.applix.com
		index index.html
	}
}
```

### Step 5 (Create Playbook Task 3)

#### Edit playbook called 5-web-server.yml

```bash
$ sudo nano 5-web-server.yml
```

##### 5-web-server.yml

```yaml
- name: Install & Configure Web Server
  hosts: web
  become: yes
  
# Create default web
  tasks:
  - name: Install nginx
    apt:
     name: nginx
     state: present
  - name: Copy HTML index file
    template:
     src: /etc/ansible/templates/index.j2
     dest: /var/www/html/index.html
  - name: Copying HTML Sites Configuration
    template:
     src: /etc/ansible/templates/default.j2
     dest: /etc/nginx/sites-available/default
  - name: Copying HTML index
    template:
     src: /etc/ansible/templates/nginx.j2
     dest: /etc/nginx/sites-available/default
# Create Intranet web
  - name: Creating Directory for Intranet Web
    file:
     path: /var/www/intranet.applix.com
     state: directory
  - name: Copying HTML Site Configuration
    template:
     src: /etc/ansible/templates/intranet-site.j2
     dest: /etc/nginx/sites-enabled/intranet
  - name: Copying HTML index file
    template:
     src: /etc/ansible/templates/intranet-index.j2
     dest: /var/www/intranet.applix.com/index.html
    notify: Restarting Webserver
  
  handlers:
  - name: Restarting Webserver
    service:
     name: nginx
     state: restarted
```

#### Verifikasi & Coba Run Playbook

```bash
$ sudo ansible-playbook 5-web-server.yml
```

Coba untuk cek web. Buka browser dan masukan IP address serta specific port

```plaintext
http://10.22.0.2:8081
http://10.22.0.3:8081
```









