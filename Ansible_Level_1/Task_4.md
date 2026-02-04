# Task 4: Copy Data to App Servers using Ansible

The Nautilus DevOps team needs to copy data from the jump host to all application servers in Stratos DC using Ansible. Execute the task with the following details:

a. Create an inventory file `/home/thor/ansible/inventory` on jump_host and add all application servers as managed nodes.

b. Create a playbook `/home/thor/ansible/playbook.yml` on the jump host to copy the `/usr/src/security/index.html` file to all application servers, placing it at `/opt/security`.

Note: Validation will run the playbook using the command ansible-playbook -i inventory playbook.yml. Ensure the playbook functions properly without any extra arguments.


## Solution
1. Create an inventory file with following contents
```
[appservers]
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_ssh_common_args='-o StrictHostKeyChecking=no'

stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'

stapp03  ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

2. Create a playbook with following contents
```
---
- name: Copy index.html to all application servers
  hosts: appservers
  become: yes

  tasks:
    - name: Ensure destination directory exists
      file:
        path: /opt/security
        state: directory
        mode: '0755'

    - name: Copy index.html to /opt/devops
      copy:
        src: /usr/src/security/index.html
        dest: /opt/security/index.html
        owner: root
        group: root
        mode: '0644'
```

3. Run the playbook
```
ansible-playbook -i inventory playbook.yml
```

4. Verify by logging into all app servers and check the copied file in `/opt/security`

    * ssh tony@172.16.238.10, cat /opt/security/blog.txt
    * ssh steve@172.16.238.11, cat /opt/security/story.txt
    * ssh banner@172.16.238.12, cat /opt/security/media.txt
