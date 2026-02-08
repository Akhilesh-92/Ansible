# Task 1: Creating Soft Links Using Ansible

The Nautilus DevOps team is practicing some of the Ansible modules and creating and testing different Ansible playbooks to accomplish tasks. Recently they started testing an Ansible file module to create soft links on all app servers. Below you can find more details about it.

Write a playbook.yml under `/home/thor/ansible` directory on jump host, an inventory file is already present under `/home/thor/ansible` directory on jump host itself. Using this playbook accomplish below given tasks:

* Create an empty file `/opt/dba/blog.txt` on `app server 1`; its user owner and group owner should be `tony`. Create a symbolic link of source path `/opt/dba` to destination `/var/www/html`.

* Create an empty file `/opt/dba/story.txt` on `app server 2`; its user owner and group owner should be `steve`. Create a symbolic link of source path `/opt/dba` to destination `/var/www/html`.

* Create an empty file `/opt/dba/media.txt` on `app server 3`; its user owner and group owner should be `banner`. Create a symbolic link of source path `/opt/dba` to destination `/var/www/html`.

Note: Validation will try to run the playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure playbook works this way without passing any extra arguments.


## Solution
1. Create a playbook.yml and add below contents
```
---
- name: Create an empty file on app server 1
  hosts: stapp01
  become: yes

  tasks:
    - name: Create an empty file /opt/dba/blog.txt
      file:
        path: /opt/dba/blog.txt
        state: touch
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
        mode: '0777'
    - name: Create symbolic link from /opt/dba to /var/www/html
      file:
        src: /opt/dba
        dest: /var/www/html
        state: link
        force: yes

- name: Create an empty file on app server 2
  hosts: stapp02
  become: yes

  tasks:
    - name: Create an empty file /opt/dba/story.txt
      file:
        path: /opt/dba/story.txt
        state: touch
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
        mode: '0777'
    - name: Create symbolic link from /opt/dba to /var/www/html
      file:
        src: /opt/dba
        dest: /var/www/html
        state: link
        force: yes

- name: Create an empty file on app server 3
  hosts: stapp03
  become: yes

  tasks:
    - name: Create an empty file /opt/dba/media.txt
      file:
        path: /opt/dba/media.txt
        state: touch
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
        mode: '0777'
    - name: Create symbolic link from /opt/dba to /var/www/html
      file:
        src: /opt/dba
        dest: /var/www/html
        state: link
        force: yes
```

2. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

3. Verify the blank file creation and symbolic link by logging into all app servers

    * ssh tony@172.16.238.10 --> ls -lr /opt/dba/
    * ssh steve@172.16.238.11 --> ls -lr /opt/dba/
    * ssh banner@172.16.238.12 --> ls -lr /opt/dba/
