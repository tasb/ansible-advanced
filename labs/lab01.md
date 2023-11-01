# Hands-On Lab: Creating and Executing the user_setup Role in Ansible

## Contents

- [Pre-requisites](#prerequisites)
- [Objective](#objective)
- [Step 0: Prepare the Environment](#step-0-prepare-the-environment)
- [Step 1: Initialize the Role](#step-1-initialize-the-role)
- [Step 2: Define Default Variables for the Role](#step-2-define-default-variables-for-the-role)
- [Step 3: Define the Role's Tasks](#step-3-define-the-roles-tasks)
- [Step 4: Create a Playbook to Use the Role](#step-4-create-a-playbook-to-use-the-role)
- [Step 5: Run the Playbook](#step-5-run-the-playbook)
- [Conclusion](#conclusion)

## Prerequisites

- Ansible installed on your machine.
- Access to a target server (on this case is only localhost).

## Objective
Create an Ansible role to set up a user, generate an SSH key for the user, and fetch the SSH key to the control node.

## Step 0: Prepare the Environment

Create a folder called ansible_advance_lab.

```bash
mkdir ansible_advance_lab
```

Now enter on that folder.

```bash
cd ansible_advance_lab
```

## Step 1: Initialize the Role

Create a roles directory:

```bash
mkdir roles
```

Now enter on that folder.

```bash
cd roles
```

Use the ansible-galaxy command to create a new role structure:

```bash
ansible-galaxy init user_setup
```

## Step 2: Define Default Variables for the Role

Navigate to the defaults directory of the role:

```bash
cd user_setup/defaults
```

Edit the main.yml file using a text editor (e.g., nano, vim, or any editor of your choice):

```bash
nano main.yml
```

Add the following default variables:

```yaml
---
username: newuser
group_name: newgroup
create_home: yes
home_subfolders:
  - documents
  - downloads
  - tmp
  - configs
destination_path_on_control_node: "/path/to/store/keys"
```

Save and close the file.

## Step 3: Define the Role's Tasks

Navigate to the tasks directory of the role:

```bash
cd ../tasks
```

Edit the main.yml file:

```bash
nano main.yml
```

Add the following tasks:

```yaml
---
- name: Create group if needed
  group:
    name: "{{ group_name }}"
    state: present

- name: Create user
  user:
    name: "{{ username }}"
    group: "{{ group_name }}"
    generate_ssh_key: yes
    ssh_key_bits: 2048
    ssh_key_file: ".ssh/id_rsa"
    createhome: "{{ create_home }}"

- name: Ensure home subfolders exist
  file:
    path: "/home/{{ username }}/{{ item }}"
    state: directory
    owner: "{{ username }}"
    group: "{{ group_name }}"
    mode: '0755'
  loop: "{{ home_subfolders }}"

- name: Fetch private SSH key to control node
  fetch:
    src: "/home/{{ username }}/.ssh/id_rsa"
    dest: "{{ destination_path_on_control_node }}/{{ username }}_id_rsa"
    flat: yes
```

Save and close the file.

## Step 4: Create a Playbook to Use the Role

Navigate back to your main working directory (outside of the roles/user_setup directory).

Create a new playbook called `setup_user.yml``:

```bash
nano setup_user.yml
```

Add the following content to the playbook:

```yaml
---
- hosts: localhost
  become: yes
  vars:
    users_list: 
      - john_doe
      - jane_doe
  roles:
    - role: ./roles/user_setup
      vars:
        username: john_doe
        group_name: staff
        destination_path_on_control_node: "/home/ubuntu/ssh_keys"
        create_home: yes
        home_subfolders:
          - documents
          - downloads
          - tmp
          - configs
          - projects
    - role: ./roles/user_setup
      vars:
        username: jane_doe
        group_name: staff
        destination_path_on_control_node: "/home/ubuntu/ssh_keys"
        create_home: yes
        home_subfolders:
          - documents
          - downloads

  tasks:
    - name: Ping hosts
      ping:

    - name: Check if folder exists
      ansible.builtin.stat:
        path: "/home/{{ item }}"
      register: result
      loop: "{{ users_list }}"
```

Save and close the file.

## Step 5: Run the Playbook

Execute the playbook using the following command:

```bash
ansible-playbook localhost setup_user.yml
```

If prompted, enter the SSH password for the target server.

## Conclusion

You've successfully created an Ansible role named user_setup and executed it using a playbook. This role sets up a user, generates an SSH key for the user, and fetches the SSH key to the control node. This hands-on lab provides a foundational understanding of Ansible roles and playbooks for novice users.