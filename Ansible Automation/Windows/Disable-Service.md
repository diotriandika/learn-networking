# Security & Logging on Windows with Ansible Playbook

**Tasks:**

- Create a playbook called 2-sec-log.yml for configuring security settings
  - Stop and disable the Remote Desktop Service on all Windows Hosts

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

## Sop and Disable Service on Windows with Ansible Playbook Steps

### Step 1 (Basic Configuration)

inventory

```ini
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

2-sec-log.yml

```yaml
---
- name: Stop & Disable Remote Desktop Services 
  hosts: WIN
  gather_facts: true
  tasks:
  - name: Stopping & Disabling the Remote Desktop Services
    win_service:
     name: TermService
     state: stopped
     start_mode: disabled
    ignore_errors: yes
```

