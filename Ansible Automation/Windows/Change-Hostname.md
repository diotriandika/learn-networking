# Configure Hostname on Windows with Ansible Playbook

**Tasks:**

- Create a playbook called 1-hostname.yml for configuring the hostname.
  - All hosts should receive the hostname based on the "hostname" variable in /etc/ansible/hosts file.

**Prequisites:**

- IP Address Configured
- Repository Updated
- WinRM Configured

**Appendix:** 

| Device ID | IP             | Description     |
| --------- | -------------- | --------------- |
| HOST      | 10.22.0.50/24  | Controller Node |
| WIN       | 10.22.0.101/24 | Managed Node    |
| WIN-DEV   | 10.22.0.252/24 | Managed Node    |

## Configuring Hostname on Windows with Ansible Playbook Steps

### Step 1 (Basic Configuration)

```ini
## Inventory
[WIN]
WIN ansible_host=10.22.0.101
WIN-DEV ansible_host=10.22.0.252
[WIN:vars]
ansible_user=ansbile
ansible_password=P@ssw0rd
ansible_port=5986
ansible_connection=winrm
ansible_winrm_transport=basic
ansible_winrm_server_cert=ignore
```

### Step 2 (Create Playbook)

1-hostname.yml

```yaml
---
- name: Configure Hostname
  hosts: WIN
  gather_facts: false
  tasks:
  - name: Changing Hostname
    win_hostname:
     name: {{ inventory_hostname }}
    register: win_hostname
  - name: Restarting Node
    win_reboot:
    when: win_hostname. reboot_required
```

Run playbook
