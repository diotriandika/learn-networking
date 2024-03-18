# Pembahasan Pre-Test Modul C (Infrastructure Programmability & Automation) Part 2

## General Configuration 

### Step 1 - Create Directory

```bash
debian@HOST:/etc/ansible$ sudo mkdir windows
```

## Hostname

### Step 1 - Create Playbook

```bash
debian@HOST:/etc/ansible/windows$ sudo nano 1-win-hostname.yml
```

**1-win-hostname.yml>**

```yaml
---
- name: Configure WIN Hostname
  hosts: WIN
  gather_facts: false
  vars_files: /etc/ansible/.vault_pass
  tasks:
  - name: Configuring Hostname
    win_hostname:
     name: "{{ hostname }}"
    register: win_hostname
  - name: Rebooting System
    win_reboot:
    when: win_hostname. reboot_required
```

**Run Playbook**

## Security & Logging

### Step 1 - Create Playbook

```bash
debian@HOST:/etc/ansible/windows$ sudo nano 2-sec-log.yml
```

**2-sec-log.yml>**

```yaml
---
- name: Disabling Remote Desktop Service
  hosts: WIN
  gather_facts: false
  vars_files: /etc/ansible/.vault_pass
  tasks:
  - name: Disabling Remote Desktop Service
    win_service:
     name: TermService
     state: stopped
     start_mode: disabled
    ignore_errors: yes
```

## Customer Deployments

### Step 1 - Create Playbook (3-environment.yml)

```bash
debian@HOST:/etc/ansible/windows$ sudo nano 3-environment.yml
```

**3-environment.yml>**

```yaml
---
- name: Install & Configure AD-DS & DNS
  hosts: WIN
  gather_facts: false 
  vars_files: /etc/ansible/.vault_pass
  tasks: 
  - name: Installing AD-DS
    win_feature:
     name: AD-Domain-Services
     include_management_tools: yes
     include_sub_features: yes
     state: present
    when: "'dc' in group_names"
  - name: Configuring Domain Controller
    win_domain:
     dns_domain_name: customers.com
     safe_mode_password: P@ssw0rd
    when: "'dc' in group_names"
  - name: Installing DNS Server
    win_feature:
     name: DNS
     state: present
    when: "'dc' in group_names"
  - name: Installing IIS
    win_feature: 
     name: Web-Server
     state: present
    when: "'iis' in group_names"
```

**Run Playbook**

### Step 2 - Create Playbook (4-customers.yml)
