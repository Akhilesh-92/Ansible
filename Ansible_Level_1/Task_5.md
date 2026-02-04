# Task 5: Create Files on App Servers using Ansible

The Nautilus DevOps team is testing various Ansible modules on servers in Stratos DC. They're currently focusing on file creation on remote hosts using Ansible. Here are the details:

a. Create an inventory file `~/playbook/inventory` on jump host and include all app servers.

b. Create a playbook `~/playbook/playbook.yml` to create a blank file `/opt/app.txt` on all app servers.

c. Set the permissions of the `/opt/app.txt` file to `0644`.

d. Ensure the `user/group` owner of the `/opt/app.txt` file is `tony` on `app server 1`, `steve` on `app server 2` and `banner` on `app server 3`.

Note: Validation will execute the playbook using the command `ansible-playbook -i inventory playbook.yml`, so ensure the playbook functions correctly without any additional arguments.


## Solution
1. Create an inventory file and add following contents
```
[appservers]
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_ssh_common_args='-o StrictHostKeyChecking=no'

stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'

stapp03  ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

2. Create a playbook and add following contents
```
---
- name: Create blank file on all app servers
  hosts: all
  become: yes

  tasks:
    - name: Create a blank file /opt/app.txt
      file:
        path: /opt/app.txt
        state: touch
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
        mode: '0644'
```

3. Run the playbook to create the blank file
```
ansible-playbook -i inventory playbook.yml
```

4. Verify the blank file creation and list the file stats by logging into all app servers

    * ssh tony@172.16.238.10, ls -lr /opt/app.txt
    * ssh steve@172.16.238.11, ls -lr /opt/app.txt
    * ssh banner@172.16.238.12, ls -lr /opt/app.txt
