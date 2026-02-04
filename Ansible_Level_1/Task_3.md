# Task 3: Configure Default SSH User for Ansible

The Nautilus DevOps team aims to manage all servers within the stack using Ansible, utilizing a common sudo user across all servers. They plan to use this user for various tasks on each server. While this isn't finalized, they're starting with testing. Ansible is already installed on the jump host via yum. Here's the requirement:

On the jump host, modify the default configuration of Ansible to enable the use of `john` as the default SSH user for all hosts. Ensure to make changes within Ansible's default configuration without creating a new one.


## Solution
1. Run this on jump host
```
sudo vi /etc/ansible/ansible.cfg
```
> Add or modify the remote user option


2. Set the user to john
```
[defaults]
remote_user = john
```
