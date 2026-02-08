# Task 4: Ansible Setup Httpd and PHP

Nautilus Application development team wants to test the Apache and PHP setup on one of the app servers in Stratos Datacenter. They want the DevOps team to prepare an Ansible playbook to accomplish this task. Below you can find more details about the task.

There is an inventory file `~/playbooks/inventory` on jump host.

Create a playbook `~/playbooks/httpd.yml` on jump host and perform the following tasks on `App Server 3`.

a. Install `httpd` and `php` packages (whatever default version is available in yum repo).

b. Change default document root of Apache to `/var/www/html/myroot` in default Apache config `/etc/httpd/conf/httpd.conf`. Make sure `/var/www/html/myroot` path exists (if not please create the same).

c. There is a template `~/playbooks/templates/phpinfo.php.j2` on jump host. Copy this template to the Apache document root you created as `phpinfo.php` file and make sure user owner and the group owner for this file is `apache` user.

d. Start and enable `httpd` service.

Note: Validation will try to run the playbook using command `ansible-playbook -i inventory httpd.yml`, so please make sure the playbook works this way without passing any extra arguments.


## Solution
1. Create a playbook with below contents
```
---
- name: Install a package using yum
  hosts: stapp03
  become: yes

  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: present

    - name: Install php package
      yum:
        name: php
        state: present
    
    - name: Create /var/www/html/myroot directory
      file:
        path: /var/www/html/myroot
        state: directory
        owner: apache
        group: apache
        mode: '0755'
    
    - name: Change Apache default document root
      replace:
        path: /etc/httpd/conf/httpd.conf
        regexp: '^DocumentRoot\s+"\/var\/www\/html"$'
        replace: 'DocumentRoot "/var/www/html/myroot"'
      notify: restart apache

    - name: Update Apache directory configuration
      replace:
        path: /etc/httpd/conf/httpd.conf
        regexp: '<Directory "/var/www/html">'
        replace: '<Directory "/var/www/html/myroot">'
      notify: restart apache
    
    - name: Deploy index.html from Jinja2 template on appserver3
      template:
        src: phpinfo.php.j2
        dest: /var/www/html/myroot/phpinfo.php
        mode: '0777'
        owner: apache
        group: apache

    - name: Ensure httpd service is running
      service:
        name: httpd
        state: started
        enabled: yes

  handlers:
    - name: restart apache
      service:
        name: httpd
        state: restarted
```

2. Run the playbook 
```
ansible-playbook -i inventory httpd.yml
```

3. Verify the installed packages httpd and PHP by logging into app server 3

    * ssh banner@172.16.238.12 --> cat /var/www/html/myroot/phpinfo.php --> ls -lr /var/www/html/myroot/ --> systemctl status httpd --> php --version
