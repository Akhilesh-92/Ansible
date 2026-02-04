# Task 1: Install Ansible

An Ansible playbook needs completion on the jump host, where a team member left off. Below are the details:

The inventory file `/home/thor/ansible/inventory` requires adjustments. The playbook must run on `App Server 3` in `Stratos DC`. Update the inventory accordingly.

Create a playbook `/home/thor/ansible/playbook.yml`. Include a task to create an empty file `/tmp/file.txt` on `App Server 3`.

Note: Validation will run the playbook using the command `ansible-playbook -i inventory playbook.yml`. Ensure the playbook works without any additional arguments.


## Solution
1. Update the inventory file with following contents:
```
stapp03  ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

2. Create a playbook.yml file and add below contents
```
---
- name: Create empty file on target hosts
  hosts: all
  become: yes

  tasks:
    - name: Create an empty file /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch
```

3. Run the playbook.yml
```
ansible-playbook -i inventory playbook.yml
```

4. Verify the file creation by logging into `App Server 3`
```
ssh banner@172.16.238.12
ls -lr /tmp/
```
