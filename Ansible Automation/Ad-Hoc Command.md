# Ad-hoc commands
Ad-Hoc command is the one-liner ansible command that performs one task on the target host (one or more managed nodes). Ad hoc commands are quick and easy, but they are not reusable. Ad-hoc commands are great for tasks you repeat rarely. An ad-hoc command looks like this:

```bash
ansible [pattern] -m [module] -a "[module options]"
```

**Use cases for ad-hoc tasks**

- Rebooting servers

```bash
ansible atlanta -a "/sbin/reboot" -f 10
```

- Managing files

```bash
ansible atlanta -m ansible.builtin.copy -a "src=/etc/hosts dest=/tmp/hosts"

ansible webservers -m ansible.builtin.file -a "dest=/srv/foo/b.txt mode=600 owner=mdehaan group=mdehaan"
```

- Managing packages

```bash
ansible webservers -m ansible.builtin.yum -a "name=acme state=present"
```

- Managing users and groups

```bash
ansible all -m ansible.builtin.user -a "name=foo password=<crypted password here>"
```

- Managing services

```bash
ansible webservers -m ansible.builtin.service -a "name=httpd state=started"
ansible webservers -m ansible.builtin.service -a "name=httpd state=restarted"
ansible webservers -m ansible.builtin.service -a "name=httpd state=stopped"
```

- Gathering facts

```bash
ansible all -m ansible.builtin.setup
```

