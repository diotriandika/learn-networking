# Inventory
Inventory contains a list of managed nodes. An inventory file is also sometimes called a “hostfile”. Your inventory can specify information like IP address for each managed node.

The default location for inventory is a file called /etc/ansible/hosts. You can specify a different inventory file at the command line using the -i option. Below is inventory with two format ini file and yaml file :

INI Format

```ini
mail.example.com

[webservers]
foo.example.com
bar.example.com

[dbservers]
one.example.com
two.example.com
three.example.com
```

Example of an Ansible inventory configured in YAML format:

```yaml
all:
  hosts:
    webserver1:
      ansible_host: 192.168.1.101
      ansible_user: myuser
      my_variable: value_for_webserver1
    webserver2:
      ansible_host: 192.168.1.102
      ansible_user: myuser
    dbserver1:
      ansible_host: 192.168.1.103
      ansible_user: myuser
    dbserver2:
      ansible_host: 192.168.1.104
      ansible_user: myuser
  children:
    web_servers:
      hosts:
        webserver1:
        webserver2:
    database_servers:
      hosts:
        dbserver1:
        dbserver2:
```

In this YAML-style inventory:

- The `all` group contains all hosts.
- Each host is defined under the `hosts` section with its hostname, SSH connection details (`ansible_host` and `ansible_user`), and any custom variables you want to define (e.g., `my_variable` for `webserver1`).
- The `children` section groups hosts into categories. In this example, there are two groups: `web_servers` and `database_servers`. You can add hosts to these groups as needed.

You can use this YAML inventory file with your Ansible playbooks by specifying it with the `-i` option when running Ansible commands:

```bash
ansible-playbook -i inventory.yml your_playbook.yml
```

This YAML inventory format provides a structured and readable way to define your inventory and host variables. You can easily expand it to include more hosts, groups, and variables as your infrastructure grows.
