# Task 5: Ansible Replace Module

There is some data on all app servers in Stratos DC. The Nautilus development team shared some requirement with the DevOps team to alter some of the data as per recent changes they made. The DevOps team is working to prepare an Ansible playbook to accomplish the same. Below you can find more details about the task.

Write a playbook.yml under `/home/thor/ansible` on jump host, an inventory is already present under `/home/thor/ansible` directory on Jump host itself. Perform below given tasks using this playbook:

* We have a file `/opt/security/blog.txt` on `app server 1`. Using Ansible replace module replace string `xFusionCorp` to `Nautilus` in that file.

* We have a file `/opt/security/story.txt` on `app server 2`. Using Ansiblereplace module replace the string `Nautilus` to `KodeKloud` in that file.

* We have a file `/opt/security/media.txt` on `app server 3`. Using Ansible replace module replace string `KodeKloud` to `xFusionCorp` Industries in that file.

Note: Validation will try to run the playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure the playbook works this way without passing any extra arguments.


## Solution
1. Create a playbook.yml with below contents
```
---
- name: Replace string in blog.txt file
  hosts: stapp01
  become: yes
  
  tasks:
    - name: Replace 'xFusionCorp' with 'Nautilus' in /opt/security/blog.txt
      replace:
        path: /opt/security/blog.txt
        regexp: 'xFusionCorp'
        replace: 'Nautilus'

- name: Replace string in story.txt file
  hosts: stapp02
  become: yes
  
  tasks:
    - name: Replace 'Nautilus' with 'KodeKloud' in /opt/security/story.txt
      replace:
        path: /opt/security/story.txt
        regexp: 'Nautilus'
        replace: 'KodeKloud'

- name: Replace string in media.txt file
  hosts: stapp03
  become: yes
  
  tasks:
    - name: Replace 'KodeKloud' with 'xFusionCorp Industries' in /opt/security/media.txt
      replace:
        path: /opt/security/media.txt
        regexp: 'KodeKloud'
        replace: 'xFusionCorp Industries'
```

2. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

3. Verify the replaced text by logging into all app servers

    * ssh tony@172.16.238.10 --> cat /opt/security/blog.txt
    * ssh steve@172.16.238.11 --> cat /opt/security/story.txt
    * ssh banner@172.16.238.12 --> cat /opt/security/media.txt
