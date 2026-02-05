# Task 1: Ansible Ping Module Usage

The Nautilus DevOps team is planning to test several Ansible playbooks on different app servers in Stratos DC. Before that, some pre-requisites must be met. Essentially, the team needs to set up a password-less SSH connection between Ansible controller and Ansible managed nodes. One of the tickets is assigned to you; please complete the task as per details mentioned below:

a. Jump host is our Ansible controller, and we are going to run Ansible playbooks through `thor` user from jump host.

b. There is an inventory file `/home/thor/ansible/inventory` on jump host. Using that inventory file test Ansible `ping` from jump host to `App Server 2`, make sure ping works.


## Solution
1. Generate a rsa key and copy it to all app servers
```
ssh-keygen -t rsa -b 2048
ssh-copy-id tony@172.16.238.10
ssh-copy-id steve@172.16.238.11
ssh-copy-id banner@172.16.238.12
```

2. Update inventory file with `ansible_user=steve` parameter for app server 2.

3. Verify the ping works
```
thor@jumphost ~/ansible$ ansible stapp02 -i inventory -m ping
stapp02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```
