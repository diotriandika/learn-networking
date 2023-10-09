Add to your environment the path to the file to store the vault password (to make the change permanent, add this line to your ~/.bashrc file):
```
export ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_pass.txt
```
Write the vault password to that file:
```
echo "my_password" > ~/.vault_pass.txt
```
Run your ansible playbook without the option --ask-vault-pass:
```
ansible-playbook my_playbook.yml
```
REFERENCES:

> Setting a default password source
> 
> If you don’t want to provide the password file on the command line or if you use one vault password file more frequently than any other, you can set the DEFAULT_VAULT_PASSWORD_FILE config option or the ANSIBLE_VAULT_PASSWORD_FILE environment variable to specify a default file to use. For example, if you set ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_pass.txt, Ansible will automatically search for the password in that file. This is useful if, for example, you use Ansible from a continuous integration system such as Jenkins.
> 
> The file that you reference can be either a file containing the password (in plain text), or it can be a script (with executable permissions set) that returns the password.
> 
> Ansible docs: https://docs.ansible.com/ansible/latest/user_guide/vault.html#setting-a-default-password-source

