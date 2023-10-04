To create an Ansible playbook for configuring two or more web servers as described, you can follow these steps:

1. Create a directory to organize your Ansible project, if you haven't already:

```shell
mkdir ansible-web-playbook
cd ansible-web-playbook
```

2. Inside this directory, create the `5-web-server.yml` playbook file:

```shell
touch 5-web-server.yml
```

3. Edit the `5-web-server.yml` playbook file using a text editor of your choice (e.g., `nano`, `vim`, or `gedit`) to define the tasks for your web servers:

```yaml
---
- name: Configure Web Servers
  hosts: web
  become: true
  vars:
    webport: "{{ hostvars[inventory_hostname]['webport'] }}"
    webcolor: "{{ hostvars[inventory_hostname]['webcolor'] }}"
  tasks:
    - name: Install web service
      apt:
        name: apache2  # Replace with the package name for your web service (e.g., nginx, httpd)
        state: present  # Ensure the web service is installed
      become: true

    - name: Configure web server
      template:
        src: index.html.j2
        dest: /var/www/html/index.html
      notify: Restart Web Service

    - name: Display custom content
      lineinfile:
        path: /var/www/html/index.html
        line: |
          Hello from {{ inventory_hostname }} !
      notify: Restart Web Service

  handlers:
    - name: Restart Web Service
      service:
        name: apache2  # Replace with the service name for your web service (e.g., nginx, httpd)
        state: restarted
```

In this playbook:

- We define a playbook called "Configure Web Servers" that targets hosts in the "web" group.
- We set variables `webport` and `webcolor` based on the values in the Ansible inventory file (usually `/etc/ansible/hosts`).
- We have three tasks:
  - The first task installs the web service (replace `apache2` with your preferred web service package name like `nginx` or `httpd`).
  - The second task uses a Jinja2 template (`index.html.j2`) to create an HTML file with the customized content and places it in the web server's document root.
  - The third task adds the "Hello from hostname" message to the HTML file.

4. Create a Jinja2 template file named `index.html.j2` in the same directory as your playbook with the following content:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Welcome to our website</title>
</head>
<body style="color: {{ webcolor }};">
  <h1>Hello from {{ inventory_hostname }} !</h1>
</body>
</html>
```

5. Ensure you have an inventory file (typically `/etc/ansible/hosts`) that defines your web servers and includes the necessary variables. For example:

```ini
[web]
webserver1 ansible_host=192.168.1.101 webport=80 webcolor=blue
webserver2 ansible_host=192.168.1.102 webport=8080 webcolor=green
```

Make sure to replace the IP addresses and variables with the appropriate values for your environment.

6. Run the Ansible playbook using the `ansible-playbook` command:

```shell
ansible-playbook 5-web-server.yml
```

This playbook will configure the web servers with the specified web service, customized content, and text color based on
