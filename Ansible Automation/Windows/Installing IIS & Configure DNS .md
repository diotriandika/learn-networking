# Customer Deployments 1

**Tasks:**

- Create a playbook called 3-environment.yml for installing the customer environment
  - Configure all servers in group "dc" as DNS Server on all WIN hosts
  - Configure all servers in group "dc" as domain controller
    - Use customers.com as domain name
    - Use "P@ssw0rd" as safe password
  - Install IIS feature in group "iis"

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

## Installing & Configuring Service on Windows Server with Playbook

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
[dc]
WIN
[iis]
WIN
```

### Step 2 (Create Playbook)

3-environment.yml

```yaml
---
- name: Instal & Configure ADDS IIS
  hosts: all
  gather_facts: yes
  
  tasks: 
  - name: Configuring DNS on Interfaces
    win_dns_client:
     adapter_names:
     - Ethernet0
     ipv4_addresses:
     - 10.22.0.101
  - name: Installing ADDS
    win_feature:
     name: AD-Domain-Services
     include_management_tools: yes
     include_sub_features: yes
     state: present
    when: "'dc' in group_names"
  - name: Configuring DNS Forest
    win_domain:
     dns_domain_name: customers.com
     safe_mode_password: "P@ssw0rd"
     when: "'dc' in group_names"
  - name: Installing IIS
    win_feature:
     name: Web-Server
     state: present
    register: complete
    when: "'dc' in group_names"
  - name: Rebooting System
    win_reboot:
```

run playbook
