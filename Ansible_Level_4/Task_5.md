# Task 5: Using Ansible Conditionals

The Nautilus DevOps team had a discussion about, how they can train different team members to use Ansible for different automation tasks. There are numerous ways to perform a particular task using Ansible, but we want to utilize each aspect that Ansible offers. The team wants to utilise Ansible's conditionals to perform the following task:

An inventory file is already placed under `/home/thor/ansible` directory on jump host, with all the Stratos DC app servers included.

Create a playbook `/home/thor/ansible/playbook.yml` and make sure to use Ansible's `when` conditionals statements to perform the below given tasks.

* Copy `blog.txt` file present under `/usr/src/itadmin` directory on jump host to `App Server 1` under `/opt/itadmin` directory. Its user and group owner must be user `tony` and its permissions must be `0744`.

* Copy `story.txt` file present under `/usr/src/itadmin` directory on jump host to `App Server 2` under `/opt/itadmin` directory. Its user and group owner must be user `steve` and its permissions must be `0744` .

* Copy `media.txt` file present under `/usr/src/itadmin` directory on jump host to `App Server 3` under `/opt/itadmin` directory. Its user and group owner must be user `banner` and its permissions must be `0744`.

NOTE: You can use ansible_nodename variable from gathered facts with when condition. Additionally, please make sure you are running the play for all hosts i.e use `- hosts: all`.

Note: Validation will try to run the playbook using command `ansible-playbook -i inventory playbook.yml`, so please make sure the playbook works this way without passing any extra arguments.


## Solution
1. Create playbook.yml with below contents
```
---
- name: Copy different files to different servers
  hosts: all
  gather_facts: yes
  become: yes

  vars:
    file_copy_map:
      stapp01:
        src: /usr/src/itadmin/blog.txt
        dest: /opt/itadmin
        owner: "tony"
        group: "tony"
        mode: '0744'

      stapp02:
        src: /usr/src/itadmin/story.txt
        dest: /opt/itadmin
        owner: "steve"
        group: "steve"
        mode: '0744'

      stapp03:
        src: /usr/src/itadmin/media.txt
        dest: /opt/itadmin
        owner: "banner"
        group: "banner"
        mode: '0744'

  tasks:

    - name: Copy file based on hostname using when condition
      copy:
        src: "{{ item.value.src }}"
        dest: "{{ item.value.dest }}"
        owner: "{{ item.value.owner }}"
        group: "{{ item.value.group }}"
        mode: "{{ item.value.mode }}"
      loop: "{{ file_copy_map | dict2items }}"
      when: item.key | lower == ansible_hostname | lower
```

2. Update inventory like below
```
[all]
stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n ansible_user=tony
stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n ansible_user=banner
```

3. Run the playbook 
```
ansible-playbook -i inventory playbook.yml
```

4. Verify the copied file by logging into all app servers

    * ssh tony@172.16.238.10 --> ls -lr /opt/itadmin/ --> cat /opt/itadmin/blog.txt
    * ssh steve@172.16.238.11 --> ls -lr /opt/itadmin/ --> cat /opt/itadmin/story.txt
    * ssh banner@172.16.238.12 --> ls -lr /opt/itadmin/ --> cat /opt/itadmin/media.txt
