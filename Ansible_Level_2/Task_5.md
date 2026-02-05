# Task 5: Ansible Blockinfile Module

The Nautilus DevOps team wants to install and set up a simple httpd web server on all app servers in Stratos DC. Additionally, they want to deploy a sample web page for now using Ansible only. Therefore, write the required playbook to complete this task. Find more details about the task below.

* We already have an inventory file under `/home/thor/ansible` directory on jump host. Create a playbook.yml under `/home/thor/ansible` directory on jump host itself.

* Using the playbook, install `httpd` web server on all app servers. Additionally, make sure its service should up and running.

* Using `blockinfile` Ansible module add some content in `/var/www/html/index.html` file. Below is the content:
```
Welcome to XfusionCorp!
This is  Nautilus sample file, created using Ansible!
Please do not modify this file manually!
```

* The `/var/www/html/index.html` file's user and group owner should be `apache` on all app servers.

* The `/var/www/html/index.html` file's permissions should be `0744` on all app servers.

Note:

i. Validation will try to run the playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure the playbook works this way without passing any extra arguments.

ii. Do not use any custom or empty marker for blockinfile module.


## Solution
1. Add `[appservers]` in inventory file at the starting of file.

2. Create a playbook.yml with following contents
```
---
- name: Install a package using yum
  hosts: all
  become: yes

  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: present
    
    - name: Ensure httpd service is running
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Insert a block into /var/www/html/index.html
      blockinfile:
        path: /var/www/html/index.html
        create: yes
        block: |
          Welcome to XfusionCorp!
          This is  Nautilus sample file, created using Ansible!
          Please do not modify this file manually!

    - name: Set file owner, group and permissions
      file:
        path: /var/www/html/index.html
        owner: apache
        group: apache
        mode: '0744'
```

3. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

4. Verify the `httpd` package installation and contents of index.html by logging into all app servers

    * ssh tony@172.16.238.10 --> systemctl status httpd --> ls -lr /var/www/html
    * ssh steve@172.16.238.11 --> systemctl status httpd --> ls -lr /var/www/html
    * ssh banner@172.16.238.12 --> systemctl status httpd --> ls -lr /var/www/html
