# Microservices

* [Ansible vault](#ansible-vault)

## Ansible vault
Ansible Vault encrypts variables and files so you can protect sensitive content such as passwords or keys 
rather than leaving it visible as plaintext in playbooks or roles. 
To use Ansible Vault you need one or more passwords to encrypt and decrypt content.

### Encrypting files with Ansible Vault

Manual enter password
```shell
ansible-vault create foo.yml
```

To create a new encrypted data file called ‘foo.yml’ with the ‘test’ vault password from ‘multi_password_file’:
```shell
ansible-vault create --vault-id test@multi_password_file foo.yml
```

To view encrypted file
```shell
ansible-vault view foo.yml
```
To edit
```shell
ansible-vault edit foo.yml
```
Change password
```shell
ansible-vault rekay foo.yml
```

### Decrypting files during Playbook Runtime
Using `--ask-vault-pass`
```shell
sudo ansible-playbook -i ansible/inventory sample-playbook.yaml –ask-vault-pass
```
Using password file
```shell
sudo ansible-playbook -i ansible/inventory sample-playbook.yaml –vault-pass-file ~/secrets/vault_pass.txt
```