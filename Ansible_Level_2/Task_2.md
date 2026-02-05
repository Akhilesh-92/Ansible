# Task 2: Ansible Install Package

The Nautilus Application development team wanted to test some applications on app servers in Stratos Datacenter. They shared some pre-requisites with the DevOps team, and packages need to be installed on app servers. Since we are already using Ansible for automating such tasks, please perform this task using Ansible as per details mentioned below:

1. Create an inventory file `/home/thor/playbook/inventory` on jump host and add all app servers in it.

2. Create an Ansible playbook `/home/thor/playbook/playbook.yml` to install `zip` package on all app servers using Ansible `yum` module.

3. Make sure user `thor` should be able to run the playbook on jump host.

Note: Validation will try to run playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure playbook works this way, without passing any extra arguments.


## Solution
1. Create an inventory file and add below contents
```
[appservers]
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_ssh_common_args='-o StrictHostKeyChecking=no'

stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'

stapp03  ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```


2. Create an Ansible playbook and add below contents
```
---
- name: Install a package using yum
  hosts: all
  become: yes

  tasks:
    - name: Install zip package
      yum:
        name: zip
        state: present
```

3. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

4. Verify the package installation by logging into all app servers

    * ssh tony@172.16.238.10, zip --version
    * ssh steve@172.16.238.11, zip --version
    * ssh banner@172.16.238.12, zip --version
  
