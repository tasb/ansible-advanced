# Ansible Vault

## Encrypt Passwords

- Create encrypted password using dev vault

```bash
ansible-vault encrypt_string 'dev_password' --name 'bd_pass' --vault-id dev@prompt
```

```bash
ansible-vault encrypt_string 'prod_password' --name 'bd_pass' --vault-id prod@prompt
```

- Let's keep password on file

```bash
echo "password" > vault_pass.txt
```

## Encrypt File

```bash
echo "API_KEY=1234567890" > confidential_data.txt
ansible-vault encrypt --vault-id dev@vault_pass.txt confidential_data.txt

echo "API_KEY=0987654321" > confidential_data.txt
ansible-vault encrypt --vault-id prod@vault_pass.txt confidential_data.txt
```

## Run Playbook

```bash
ansible-playbook vault.yml --vault-id dev@vault_pass.txt
```

```bash
ansible-playbook vault.yml --vault-id prod@vault_pass.txt
```
