# Task 3: Ansible Manage Services

Developers are looking for dependencies to be installed and run on Nautilus app servers in Stratos DC. They have shared some requirements with the DevOps team. Because we are now managing packages installation and services management using Ansible, some playbooks need to be created and tested. As per details mentioned below please complete the task:

a. On jump host create an Ansible playbook `/home/thor/ansible/playbook.yml` and configure it to install `vsftpd` on all app servers.

b. After installation make sure to start and enable `vsftpd` service on all app servers.

c. The inventory `/home/thor/ansible/inventory` is already there on jump host.

d. Make sure user `thor` should be able to run the playbook on jump host.

Note: Validation will try to run playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure playbook works this way, without passing any extra arguments.


## Solution
1. Create a playbook.yml and add below contents
```
---
- name: Install a package using yum
  hosts: all
  become: yes

  tasks:
    - name: Install vsftpd package
      yum:
        name: vsftpd
        state: present
    
    - name: Ensure vsftpd service is running
      service:
        name: vsftpd
        state: started
        enabled: yes
```

3. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

4. Verify the blank file creation by logging into all app servers

    * ssh tony@172.16.238.10 --> systemctl status vsftd
    * ssh steve@172.16.238.11 --> systemctl status vsftd
    * ssh banner@172.16.238.12 --> systemctl status vsftd
