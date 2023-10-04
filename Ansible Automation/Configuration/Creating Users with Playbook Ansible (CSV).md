# Creating Users with Playbook Ansible (CSV)

Dalam ansible, kita bisa menggunakan Playbook untuk mengimport user ke Managed Node dari file CSV (Comma Separated Value)

**Task:**

- Create a playbook called 7-users.yml for importing users on all LIN hosts
  - Import the users from /etc/ansible/users.csv on all LIN hosts
  - Make sure, that password is not changed if there is already an exiting user with same username and UID

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

## Creating Users with Playbook Ansible (CSV) Steps

### Step 1 (Basic Configuration)

#### HOST>

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
    LIN3:
     ansible_host: 10.22.0.3
     ansible_user: root
     ansible_ssh_pass: Skills39
     hostname: LIN3
```

##### Check Connectivity

```bash
$ sudo ansible -m ping all
```

### Step 2 (Create list of Users)

#### HOST>

##### Create User List in CSV file

```bash
# Move to /etc/ansible directory
$ cd /etc/ansible

# Create and Edit file named users.csv
$ sudo nano users.csv
```

###### users.csv

```plaintext
# Example
Username,UID,First_name,Last_name,Groups,Password
booker12,9012,Rachel,Booker,Operations,iambooker
grey07,2070,Laura,Grey,Developers,iamgrey
johnson81,4081,Craig,Johnson,Operations,iamjohnson
jenkins46,9346,Mary,Jenkins,Developers,iamjenkins
smith79,5079,Jamie,Smith,Operations,iamsmith
```

### Step 3 (Create Playbook)

##### Create playbook file named 7-users.yaml

```bash
$ sudo nano 7-users.yaml
```

##### 7-users.yaml file:

```yaml
# Create User Playbook
- name: Import User for LIN_Host
  host: LIN_Host
  tasks: 
  # Read CSV file with read_csv module, add path file location, register to capture output and store it in a variable, and make sure to set deligate_to: that pointing to localhost
  - name: read csv file
    read_csv:
     path: /etc/ansible/users.csv
    register: user_list
    deligate_to: localhost
  # Print Output and create new variable
  - name: print user_list data
    debug:
     var: user_list.list
  # Extract item Username and make loop with variable above
  - name: Extracting Username
    debug:
     msg: "{{ item.Username }}"
    loop: "{{ user_list.list }}"
  # Create user with Variable Output 
  - name: create user
    user:
     name: "{{ item.Username }}"
     state: present
    loop: "{{ user_list.list }}"
    become: true
```

### Verifikasi dan Coba Run Playbook

```bash
# verifikasi dulu 
$ sudo ansible-playbook 7-users.yml --check

# coba eksekusi Playbook
$ sudo ansible-playbook 7-users.yml 
```

**Referensi:**

- https://www.redhat.com/sysadmin/ansible-create-users-csv

- https://youtu.be/vd94qsmt5pA

- https://serverfault.com/questions/850497/ansible-create-users-from-csv
  
- https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_identity_management/managing-user-accounts-using-ansible-playbooks_configuring-and-managing-idm

  

