# Task 2: Managing ACLs Using Ansible

There are some files that need to be created on all app servers in Stratos DC. The Nautilus DevOps team want these files to be owned by user root only however, they also want that the app specific user to have a set of permissions on these files. All tasks must be done using Ansible only, so they need to create a playbook. Below you can find more information about the task.

Create a playbook named playbook.yml under `/home/thor/ansible` directory on jump host, an inventory file is already present under `/home/thor/ansible` directory on Jump Server itself.

* Create an empty file `blog.txt` under `/opt/finance/` directory on `app server 1`. Set some acl properties for this file. Using acl provide `read '(r)'` permissions to group `tony` (i.e entity is `tony` and etype is `group`).

* Create an empty file `story.txt` under `/opt/finance/` directory on `app server 2`. Set some acl properties for this file. Using acl provide `read + write '(rw)'` permissions to user steve (i.e entity is `steve` and etype is `user`).

* Create an empty file `media.txt` under `/opt/finance/` on `app server 3`. Set some acl properties for this file. Using acl provide `read + write '(rw)'` permissions to group banner (i.e entity is `banner` and etype is `group`).

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way, without passing any extra arguments.


## Solution
1. Create a playbook named playbook.yml with below contents
```
---
- name: Create blank file on app server 1
  hosts: stapp01
  become: yes

  tasks:
    - name: Create a blank file /opt/finance/blog.txt
      file:
        path: /opt/finance/blog.txt
        state: touch
    - name: Provide read (r) access to group tony
      acl:
        path: /opt/finance/blog.txt
        entity: tony
        etype: group
        permissions: r
        state: present

- name: Create blank file on app server 2
  hosts: stapp02
  become: yes

  tasks:
    - name: Create a blank file /opt/finance/story.txt
      file:
        path: /opt/finance/story.txt
        state: touch
    - name: Provide readwrite (rw) access to group steve
      acl:
        path: /opt/finance/story.txt
        entity: steve
        etype: user
        permissions: rw
        state: present

- name: Create blank file on app server 3
  hosts: stapp03
  become: yes

  tasks:
    - name: Create a blank file /opt/finance/media.txt
      file:
        path: /opt/finance/media.txt
        state: touch
    - name: Provide readwrite (rw) access to group banner
      acl:
        path: /opt/finance/media.txt
        entity: banner
        etype: group
        permissions: rw
        state: present
```

2. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

3. Verify the blank file creation and ACLs by logging into all app servers

    * ssh tony@172.16.238.10 --> ls -lr /opt/finance/
    * ssh steve@172.16.238.11 --> ls -lr /opt/finance/
    * ssh banner@172.16.238.12 --> ls -lr /opt/finance/
