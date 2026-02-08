# Task 4: Ansible Lineinfile Module

The Nautilus DevOps team want to install and set up a simple httpd web server on all app servers in Stratos DC. They also want to deploy a sample web page using Ansible. Therefore, write the required playbook to complete this task as per details mentioned below.

* We already have an inventory file under `/home/thor/ansible` directory on jump host. Write a playbook playbook.yml under `/home/thor/ansible` directory on jump host itself. Using the playbook perform below given tasks:

* Install `httpd` web server on all app servers, and make sure its service is up and running.

* Create a file `/var/www/html/index.html` with content:
  ```
  This is a Nautilus sample file, created using Ansible!
  ```
* Using `lineinfile` Ansible module add some more content in `/var/www/html/index.html` file. Below is the content:
  ```
  Welcome to xFusionCorp Industries!
  ```
* Also make sure this new line is added at the top of the file.

* The `/var/www/html/index.html` file's user and group owner should be `apache` on all app servers.

* The `/var/www/html/index.html` file's permissions should be `0755` on all app servers.

Note: Validation will try to run the playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure the playbook works this way without passing any extra arguments.


## Solution
1. Create a playbook.yml with below contents
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

- name: Create a new file and add contents
  hosts: all
  become: yes

  tasks:
    - name: Create a file /var/www/html/index.html
      file:
        path: /var/www/html/index.html
        state: touch
        mode: '0644'
    - name: Copy contents to html file
      copy:
        dest: /var/www/html/index.html
        content: |
          This is a Nautilus sample file, created using Ansible!

- name: Add some more content using lineinfile
  hosts: all
  become: yes

  tasks:
    - name: Add a line at the top of the file
      lineinfile:
        path: /var/www/html/index.html
        line: "Welcome to xFusionCorp Industries!"
        insertafter: BOF
        owner: apache
        group: apache
        mode: '0755'
```

2. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

3. Verify the installed httpd package by logging into all app servers

    * ssh tony@172.16.238.10, systemctl status httpd, ls -lr /var/www/html/index.html, cat /var/www/html/index.html
    * ssh steve@172.16.238.11, systemctl status httpd, ls -lr /var/www/html/index.html, cat /var/www/html/index.html
    * ssh banner@172.16.238.12, systemctl status httpd, ls -lr /var/www/html/index.html, cat /var/www/html/index.html
