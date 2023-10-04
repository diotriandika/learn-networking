# (Ansible) Configure Hostname with Variables

Dalam ansible, kita bisa menggunakan Variable 'ansible_hostname' untuk task mengkonfigurasi Hostname dari sebuah host. Variable ini dapat digunakan untuk `set and change` password ketika mengeksekusi playbook.

Contoh kasus:

- Do in HOST. Create a playbook called 1-hostname.yml for configuring hostname according to the appendix
  - Every host should be assigned a hostname, derived from the 'hostname' variable found in the /etc/ansible/hosts file.

Appendix:

| Device ID | IP            | Description     |
| --------- | ------------- | --------------- |
| HOST      | 10.22.0.50/24 | Controller Node |
| LIN1      | 10.22.0.1/24  | Managed Node    |
| LIN2      | 10.22.0.2/24  | Managed Node    |

Prequisites:

- IP Address Configured
- Repository Updated (if using live cd)

## Configuring Hostname with Variables Steps

### Step 1 (Basic Configuration)

#### ALL Nodes>

##### Install Python & SSH 

```bash
$ sudo apt-get install openssh-server & python3 # Skip if done
```

##### Enable PasswordAuthentication & PermitRootLogin

```bash
$ sudo nano -l /etc/ssh/sshd_config
---
# uncomment line 34 & 58
PermitRootLogin yes
PasswordAuthentication yes
```

##### Restart Service

```bash
$ sudo systemctl restart sshd
```

#### **HOST>**

##### Install Ansible on controller node

```bash
$ sudo apt install ansible
```

##### Setup Inventory File

```bash
$ sudo mkdir /etc/ansible
$ cd /etc/ansible
$ sudo nano hosts
```

```yaml
# Disini saya menggunakan format YML/YAML
# Mendefinisikan Group Managed Node
all:
 children:
  hosts:
   LIN1: 
    ansible_host: 10.22.0.1
    ansible_user: root
    ansible_ssh_password: Skills39
   LIN2:
    ansible_host: 10.22.0.2
    ansible_user: root
    ansible_password: Skills39

```

> Jika ingin mengecek Inventory, jalankan:
>
> ```bash
> $ ansible-inventory --list -y
> ```

##### Verification

Test koneksi ke node dengan module ping.o

```bash
$ ansible all -m ping
```

### Step 2 (Create Playbook)

#### HOST>

##### Create Ansible Playbook named 1-hostname.yml

```bash
$ sudo touch 1-hostname.yml
```

##### Edit file 1-hostname.yml to provide task Hostname configuration

```bash
$ sudo nano 1-hostname.yml
```

###### 1-hostname.yml

```yaml
# Determine the Host for Tasks
- name: Configure Hostname
  hosts: LIN_Host
  become: yes # need access user root/sudo to configuring

# Create Tasks
  tasks:
  - name: Set Hostname
    hostname:
     name: "{{ hostname }}" # Here i'm using a variables to determine the hostname, you can freely change this as your hostname exact
     become: yes # need access root/sudo to configuring
```

### Step 2 (Create Variables)

> Step ini hanya diperlukan jika kalian menggunakan variabel seperti saya diatas, namun jika tidak langsung saja ke step selanjutnya

#### HOST>

##### Create Variables on hosts file

```bash
$ sudo nano hosts # Specific path at /etc/ansible/hosts
```

###### hosts

```ini
# Disini saya menggunakan format YML/YAML
# Mendefinisikan Group Managed Node
all:
 children:
  hosts:
   LIN1: 
    ansible_host: 10.22.0.1
    ansible_user: root
    ansible_ssh_password: Skills39
    hostname: LIN1          # Tambahkan line baru untuk mengset Static Variable
   LIN2:
    ansible_host: 10.22.0.2
    ansible_user: root
    ansible_password: Skills39
    hostname: LIN2          # Tambahkan line baru untuk mengset Static Variable
    
```

### Step 3 (Verify & Try to run the playbook)

#### HOST>

##### Coba verify terlebih dahulu, apakah semua file konfigurasi sudah berjalan dengan benar atau tidak

```bash
# File Inventory
$ sudo ansible -m ping all
---output
LIN1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
LIN2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

# Playbook
$ sudo ansible-playbook 1-hostname.yml --check
---output
PLAY [Configure Hostname] **********************************************************************************************
TASK [Gathering Facts] *************************************************************************************************
ok: [LIN1]
ok: [LIN2]

TASK [Set Hostname] ****************************************************************************************************
changed: [LIN2]
changed: [LIN1]

PLAY RECAP *************************************************************************************************************
LIN1                       : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
LIN2                       : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

##### Run playbook

```bash
$ sudo ansible-playbook 1-hostname.yml
---output
---output
PLAY [Configure Hostname] **********************************************************************************************
TASK [Gathering Facts] *************************************************************************************************
ok: [LIN1]
ok: [LIN2]

TASK [Set Hostname] ****************************************************************************************************
changed: [LIN2]
changed: [LIN1]

PLAY RECAP *************************************************************************************************************
LIN1                       : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
LIN2                       : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### Verifikasi

Coba cek pada kedua Managed Nodes, apakah Hostname sudah berhasil diset atau tidak. Jika Hostname tidak berganti, coba untuk melakukan login ulang atau reboot system

Referensi:

- https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html
- https://docs.ansible.com/ansible/2.8/modules/hostname_module.html

- https://github.com/codylane/ansible-playbook-change-hostname/blob/master/change_hostname.yaml
- ChatGPT etc. just forgot to documment
