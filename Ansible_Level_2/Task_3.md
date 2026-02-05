# Task 3: Ansible Archive Module

The Nautilus DevOps team has some data on each app server in Stratos DC that they want to copy to a different location. However, they want to create an archive of the data first, then they want to copy the same to a different location on the respective app server. Additionally, there are some specific requirements for each server. Perform the task using Ansible playbook as per requirements mentioned below:

Create a playbook named playbook.yml under `/home/thor/ansible` directory on jump host, an inventory file is already placed under `/home/thor/ansible/` directory on Jump Server itself.

Create an archive `media.tar.gz` (make sure archive format is `tar.gz`) of `/usr/src/itadmin/` directory ( present on each app server ) and copy it to `/opt/itadmin/` directory on all app servers. The user and group owner of archive `media.tar.gz` should be `tony` for `App Server 1`, `steve` for `App Server 2` and `banner` for `App Server 3`.

Note: Validation will try to run playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure playbook works this way, without passing any extra arguments.


## Solution
1. Add `[appservers]` in inventory file at the starting of file.

2. Create a playbook and add below contents
```
---
- name: Archive and copy itadmin directory on app servers
  hosts: appservers
  become: yes

  tasks:

    - name: Ensure destination directory exists
      file:
        path: /opt/itadmin/
        state: directory
        mode: '0755'

    - name: Create tar.gz archive of /usr/src/itadmin directory
      archive:
        path: /usr/src/itadmin/
        dest: /usr/src/itadmin/media.tar.gz
        format: gz

    - name: Copy archive to /opt/itadmin directory
      copy:
        src: /usr/src/itadmin/media.tar.gz
        dest: /opt/itadmin/
        remote_src: yes
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
        mode: '0777'
```

3. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

4. Verify the copied archive file by logging into all app servers

    * ssh tony@172.16.238.10, ls -lr /opt/itadmin/
    * ssh steve@172.16.238.11, ls -lr /opt/itadmin/
    * ssh banner@172.16.238.12, ls -lr /opt/itadmin/
