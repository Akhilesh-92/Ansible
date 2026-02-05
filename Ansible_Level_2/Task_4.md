# Task 4: Ansible Unarchive Module

One of the DevOps team members has created a zip archive on jump host in Stratos DC that needs to be extracted and copied over to all app servers in Stratos DC itself. Because this is a routine task, the Nautilus DevOps team has suggested automating it. We can use Ansible since we have been using it for other automation tasks. Below you can find more details about the task:

* We have an inventory file under `/home/thor/ansible directory` on jump host, which should have all the app servers added already.

* There is a zip archive `/usr/src/devops/xfusion.zip` on jump host.

* Create a playbook.yml under `/home/thor/ansible/` directory on jump host itself to perform the below given tasks.

* Unzip `/usr/src/devops/xfusion.zip` archive in `/opt/devops/` location on all app servers.

* Make sure the extracted data must has the respective sudo user as their user and group owner, i.e `tony` for `app server 1`, `steve` for `app server 2`, `banner` for `app server 3`.

* The extracted data permissions must be `0777`.

Note: Validation will try to run the playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure playbook works this way, without passing any extra arguments.


## Solution
1. Add `[appservers]` in inventory file at the starting of file.

2. Create a playbook.yml and add below contents
```
---
- name: Unzip devops.zip from jump host to app servers
  hosts: appservers
  become: yes

  tasks:

    - name: Ensure destination directory exists on app servers
      file:
        path: /opt/devops/
        state: directory
        mode: '0755'

    - name: Copy devops.zip from control node to app servers
      copy:
        src: /usr/src/devops/xfusion.zip
        dest: /opt/devops/xfusion.zip

    - name: Unzip devops.zip into /opt/itadmin on app servers
      unarchive:
        src: /opt/devops/xfusion.zip
        dest: /opt/devops/
        remote_src: yes
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
        mode: '0744'
```

3. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

4. Verify the unarchived file by logging into all app servers

    * ssh tony@172.16.238.10, ls -lr /opt/devops/ and ls -lr /opt/devops/unarchive/
    * ssh steve@172.16.238.11, ls -lr /opt/devops/ and ls -lr /opt/devops/unarchive/
    * ssh banner@172.16.238.12, ls -lr /opt/devops/ and ls -lr /opt/devops/unarchive/
