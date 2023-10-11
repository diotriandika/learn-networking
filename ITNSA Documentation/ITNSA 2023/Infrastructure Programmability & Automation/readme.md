# Pembahasan Pre-Test Modul C (Infrastructure Programmability & Automation)

## Pre-Configured

**Inventory :**

```ini
[LIN]
LIN1 ansible_host=10.22.0.1 hostname=LIN1
LIN2 ansible_host=10.22.0.2 hostname=LIN2
LIN3 ansible_host=10.22.0.3 hostname=LIN3
LIN4 ansible_host=10.22.0.4 hostname=LIN4
[DEV]
DEV-LIN ansible_host=10.22.0.251 hostname=DEV-LIN
[dns]
LIN1
LIN2
[web]
LIN3 webport=8080 webcolor=green
LIN4 webport=9090 webcolor=red
[ha]
LIN3 priority=101
LIN4 priority=100
[WINDOWS]
WIN ansible_host=10.22.0.101 hostname=WIN
DEV-WIN ansible_host=10.22.0.252 hostname=DEV-WIN
[WINDOWS:vars]
ansible_port=5986
ansible_connection=winrm
ansible_winrm_transport=basic
ansible_winrm_server_cert_validation=ignore
[dc]
WIN
[iis]
WIN
```

**Encrypted Credentials :**

```yaml
# .vault_pass
ansible_ssh_user: debian
ansible_ssh_pass: P@ssw0rd
ansible_sudo_pass: P@ssw0rd

ansible_user: user
ansible_password: P@ssw0rd
```

**users.csv**
```plaintext
Username,UID,First_name,Last_name,Groups,Password
booker12,9012,Rachel,Booker,Operations,iambooker
grey07,2070,Laura,Grey,Developers,iamgrey
johnson81,4081,Craig,Johnson,Operations,iamjohnson
jenkins46,9346,Mary,Jenkins,Developers,iamjenkins
smith79,5079,Jamie,Smith,Operations,iamsmith
```
**Directory Tree :**
```
.
└── etc/
    └── ansible/
        ├── linux/
        ├── windows/
        ├── hosts
        └── .vault_pass
```

