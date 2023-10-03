# Ansible Vault (Encrypt & Decrypt Data, Password ETC)

#### Secrets

------

Ansible Vault encrypts variables and files so you can protect sensitive content such as passwords or keys rather than leaving it visible as plaintext in playbooks or roles. To use Ansible Vault you need one or more passwords to encrypt and decrypt content.

- Encrypt data

```
ansible-vault encrypt foo.yml bar.yml baz.yml
```

- View secret

```
ansible-vault view foo.yml bar.yml baz.yml
```

- Editing secret

```
ansible-vault edit foo.yml
```

- Decrypt data

```
ansible-vault decrypt foo.yml bar.yml baz.yml
```

More about secret : [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html)