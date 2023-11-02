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


ansible-vault encrypt_string 'SuperSecret123' --name 'my_secret_password'
You'll be prompted to enter a new Vault password. This password will be used to encrypt and later decrypt the secret.

The output will be a block of encrypted data. Copy this block; you can use it in your playbooks or roles.

Step 3: Encrypt a File with Ansible Vault
Create a file named secret_data.txt with some confidential information:
bash
Copy code
echo "This is a secret file!" > secret_data.txt
Encrypt the file using Ansible Vault:
bash
Copy code
ansible-vault encrypt secret_data.txt
You'll be prompted to enter the Vault password. Use the same password you set in Step 2.

The file secret_data.txt is now encrypted. If you try to view its contents, you'll see encrypted data.

Step 4: Decrypting and Viewing Secrets
To view the encrypted file's contents without decrypting the file:
bash
Copy code
ansible-vault view secret_data.txt
Enter the Vault password when prompted.

To decrypt the file entirely:
bash
Copy code
ansible-vault decrypt secret_data.txt
Enter the Vault password when prompted. The file will be decrypted, and you can view its original contents using any text editor.

Step 5: Using Encrypted Data in Playbooks
When using encrypted data in playbooks, you'll need to provide the Vault password to decrypt the data during playbook execution.

Create a playbook named vault_demo.yml:

yaml
Copy code
---
- hosts: localhost
  tasks:
    - name: Display secret password
      debug:
        var: my_secret_password
Run the playbook:
bash
Copy code
ansible-playbook vault_demo.yml --ask-vault-pass
Enter the Vault password when prompted. The playbook will display the decrypted password.

Conclusion:
In this lab, you learned how to use Ansible Vault to encrypt a password and a file, and how to decrypt and use them. Ansible Vault is a powerful tool for ensuring that sensitive data remains secure while still being accessible for automation tasks.




User
Include in the lab to use different vaults
ChatGPT
Absolutely! Let's expand the lab to demonstrate the use of multiple Ansible Vaults with different passwords.

Step 6: Using Multiple Vaults
Ansible allows you to use multiple vault passwords, which can be useful if you have different levels of secrets or if secrets are managed by different teams.

Creating a Second Vault:

Encrypt another string, but this time we'll label it with a vault ID:

bash
Copy code
ansible-vault encrypt_string 'AnotherSecret456' --name 'another_secret_password' --vault-id another_vault@prompt
You'll be prompted to enter a new Vault password for this vault ID. Let's use AnotherVaultPassword.

Storing Vault Passwords in Files:

For convenience, you can store each vault password in a separate file. This is especially useful for automation:

bash
Copy code
echo "YourFirstVaultPassword" > vault_pass.txt
echo "AnotherVaultPassword" > another_vault_pass.txt
Note: Storing passwords in plaintext files is risky. Ensure these files are protected and only readable by the necessary users. In production, consider more secure methods like password prompts or integration with secret management tools.

Using Multiple Vaults in Playbooks:

Update the vault_demo.yml playbook to include the new secret:

yaml
Copy code
---
- hosts: localhost
  tasks:
    - name: Display secret password from the first vault
      debug:
        var: my_secret_password
    
    - name: Display secret password from the second vault
      debug:
        var: another_secret_password
Running Playbook with Multiple Vault Passwords:

When running the playbook, you can specify multiple vault password files:

bash
Copy code
ansible-playbook vault_demo.yml --vault-id default@vault_pass.txt --vault-id another_vault@another_vault_pass.txt