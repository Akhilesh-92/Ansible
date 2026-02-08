# Task 2: Ansible Create Users and Groups

Several new developers and DevOps engineers just joined the xFusionCorp industries. They have been assigned the Nautilus project, and as per the onboarding process we need to create user accounts for new joinees on at least one of the app servers in Stratos DC. We also need to create groups and make new users members of those groups. We need to accomplish this task using Ansible. Below you can find more information about the task.

There is already an inventory file `~/playbooks/inventory` on jump host.

On jump host itself there is a list of users in `~/playbooks/data/users.yml` file and there are two groups — admins and developers —that have list of different users. Create a playbook `~/playbooks/add_users.yml` on jump host to perform the following tasks on app server 3 in Stratos DC.

a. Add all users given in the `users.yml` file on `app server 3`.

b. Also add developers and admins groups on the same server.

c. As per the list given in the `users.yml` file, make each user member of the respective group they are listed under.

d. Make sure home directory for all of the users under developers group is `/var/www` (not the default i.e `/var/www/{USER}`). Users under admins group should use the default home directory (i.e `/home/david` for user `devid`).

e. Set password `dCV3szSGNA` for all of the users under `developers` group and `LQfKeWWxWD` for of the users under `admins` group. Make sure to use the password given in the `~/playbooks/secrets/vault.txt` file as Ansible vault password to encrypt the original password strings. You can use `~/playbooks/secrets/vault.txt` file as a vault secret file while running the playbook (make necessary changes in `~/playbooks/ansible.cfg` file).

f. All users under admins group must be added as sudo users. To do so, simply make them member of the `wheel` group as well.

Note: Validation will try to run the playbook using command `ansible-playbook -i inventory add_users.yml` so please make sure playbook works this way, without passing any extra arguments.


## Solution
1. Update ansible.cfg like below
```
[defaults]
host_key_checking = False
vault_password_file = ~/playbooks/secrets/vault.txt
```

2. Create password_hash of password strings
```
ansible localhost -m debug -a "msg={{ 'ksH85UJjhb' | password_hash('sha512') }}"
ansible localhost -m debug -a "msg={{ 'YchZHRcLkL' | password_hash('sha512') }}"
```

3. Create encrypted string
```
ansible-vault encrypt_string 'ksH85UJjhb' --name 'dev_password'
ansible-vault encrypt_string 'YchZHRcLkL' --name 'admin_password'
```

4. Create a playbook.yml with below contents
```
---
- name: Add all users on app server 2
  hosts: stapp02
  become: yes
  vars_files:
    - /home/thor/playbooks/data/users.yml
  
  tasks:
    - name: Create developers group
      group:
        name: developers
        state: present

    - name: Create admins group
      group:
        name: admins
        state: present

    - name: Create users in developers group with /var/www as shared home directory
      user:
        name: "{{ item }}"
        group: developers
        home: "/var/www/{{ item }}"
        create_home: yes
        password: "$6$7m5eZs50o9uEqwGW$lE4IkhjFGN.ev6vCXVwLfJM5joIaNjBq7p93m/uvWWNcke.HsXwDza9xVR67Bhl42WqLdh2BV5kk//x8cB3rN/"
      vars:
        dev_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          62623131656463376239326562653066316631316563363432633561633336313339313636363034
          3662366663346138663565396230356533353561396532370a313266396661386434336434626163
          33376261616636323537336361663737346633343337313732356262633139383435386637376635
          3037346336373462360a303562623032393936343539663738376666353163333433333332313539 
        update_password: on_create
        state: present
      loop: "{{ developers }}"
    
    - name: Create users in admins group with default home directory
      user:
        name: "{{ item }}"
        group: admins
        groups: wheel
        append: yes
        create_home: yes
        password: "$6$t8IsNgaR0MQChjo8$Omx/RlQdfRtHXCu8XWT6iMmiyzvr.q6ijvgRXR.njv.SjKOs9UGnaKKNB5fwrDekS.ZO.h8LiwJukXABG1KSy/"
      vars:
        admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          33376633353661333731323434666164326130666431653565366533393432666633613331346537
          6662646239623161663630636136333964336131313966360a613730323538396534656361663062
          37363837356337396261386564666334386430323937383661613334616265653233363931636532
          3234643930326664630a363666613439316333663464303133623930393064623061376130336635      
        update_password: on_create
        state: present
      loop: "{{ admins }}"
```

5. Run the playbook
```
ansible-playbook -i inventory index.yml 
```

6. Verify all the resources are created.
