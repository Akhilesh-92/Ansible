# Task 3: Managing Jinja2 Templates Using Ansible

One of the Nautilus DevOps team members is working on to develop a role for httpd installation and configuration. Work is almost completed, however there is a requirement to add a jinja2 template for index.html file. Additionally, the relevant task needs to be added inside the role. The inventory file `~/ansible/inventory` is already present on jump host that can be used. Complete the task as per details mentioned below:

a. Update `~/ansible/playbook.yml` playbook to run the `httpd` role on `App Server 3`.

b. Create a jinja2 template `index.html.j2` under `/home/thor/ansible/role/httpd/templates/` directory and add a line `This file was created using Ansible on <respective server>` (for example This file was created using Ansible on `stapp01` in case of `App Server 1`). Also please make sure not to hard code the server name inside the template. Instead, use `inventory_hostname` variable to fetch the correct value.

c. Add a task inside `/home/thor/ansible/role/httpd/tasks/main.yml` to copy this template on `App Server 3` under `/var/www/html/index.html`. Also make sure that `/var/www/html/index.html` file's permissions are `0777`.

d. The user/group owner of `/var/www/html/index.html` file must be respective sudo user of the server (for example tony in case of `stapp01`).

Note: Validation will try to run the playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure the playbook works this way without passing any extra arguments.


## Solution
1. Create a playbook.yml with below contents
```
---
- name: Apply httpd role on servers
  hosts: stapp03 
  become: yes
  become_user: root
  roles:
    - role/httpd
```


2. Create a `index.html.j2` file and add below line
```
This file was created using Ansible on {{ inventory_hostname }}
```


3. Create a main.yml with below contents by `vi /home/thor/ansible/role/httpd/tasks/main.yml`
```
---
# tasks file for role/test

- name: install the latest version of HTTPD
  yum:
    name: httpd
    state: latest

- name: Start service httpd
  service:
    name: httpd
    state: started

- name: Deploy index.html from Jinja2 template on appserver3
  template:
    src: index.html.j2        
    dest: /var/www/html/index.html
    mode: '0777'
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
  when: inventory_hostname == "stapp03"
```

4. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

5. Verify the installed httpd package and copied content by logging into app server 3

    * ssh banner@172.16.238.12 --> ls -lr /var/www/html/index.html --> cat /var/www/html/index.html
