# # Become (Privilege Escalation)

Ansible uses existing privilege escalation systems to execute tasks with root privileges or with another user’s permissions. Because this feature allows you to ‘become’ another user, different from the user that logged into the machine (remote user), we call it become. The become keyword leverages existing privilege escalation tools like sudo, su, pfexec, doas, pbrun, dzdo, ksu, runas, machinectl and others.

Example tasks with become

```yaml
- name: Run a command as root
  command: apt update
  become: yes
  become_user: root
```