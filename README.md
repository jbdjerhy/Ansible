Ansible Automation Project Documentation
========================================

I. Installing Ansible
---------------------

### 1\. Installing Required Dependencies

bashCopy code

`sudo yum install -y epel-release
sudo yum install -y python3
sudo alternatives --set python /usr/bin/python3`

### 2\. Installing Ansible

bashCopy code

`sudo yum install -y ansible`

Installed Ansible Core version: 2.14.2

### 3\. Installing Argcomplete for Shell Completion

bashCopy code

`sudo yum install -y python3-argcomplete
sudo activate-global-python-argcomplete`

### 4\. Adding `rhhost2` to Inventory

Edit `/etc/ansible/hosts` and add `rhhost2` to the inventory.

Check with:

bashCopy code

`sudo ansible all --list-hosts`

### 5\. Generating SSH Keys

bashCopy code

`sudo ssh-keygen
ls ~/.ssh/`

Copy keys to `rhhost2`:

bashCopy code

`ssh-copy-id 10.0.2.22`

Check success:

bashCopy code

`ansible -m ping all`

### 6\. Sending Uptime Command

bashCopy code

`ansible -a "uptime" all`

### 7\. Modifying Ansible Hosts File

Edit `/etc/ansible/hosts` to add `rhhost2` to the `webserver` group.

Try ad hoc commands.

II. Using Ad Hoc Commands
-------------------------

### 1\. Creating Files with 'file' Module

bashCopy code

`ansible rhhost2 -m file -a "path=/home/user1/file2.txt state=touch mode=700"
ansible rhhost2 -m file -a "path=/home/user1/file3.txt state=touch mode=700"
ansible rhhost2* -m file -a "dest=/home/user1/file2.txt mode=600 owner=root group=root" -b -K`

### 2\. Installing HTTPD with Yum Module

bashCopy code

`ansible rhhost2* -m yum -a "name=httpd state=installed" -b -K
ansible rhhost2* -m service -a "name=httpd state=started" -b -K
ansible rhhost2* -m service -a "name=httpd state=reloaded" -b -K
ansible rhhost2* -m service -a "name=httpd state=enabled" -b -K
ansible rhhost2* -m service -a "name=httpd enabled=no" -b -K --check
ansible rhhost2* -m shell -a "systemctl status httpd"
ansible rhhost2* -m shell -a "/sbin/reboot" -b -K`

### 3\. Managing Users

bashCopy code

`ansible rhhost2* -m user -a 'name=user2 state=present home=/home/user2 shell=/bin/bash' -b -K
ansible rhhost2* -m user -a 'name=user2 group=user2 groups=wheel' -b -K
ansible rhhost2* -m user -a 'name=user2 state=absent' -b -K`

### 4\. Gathering Host Data

bashCopy code

`ansible rhhost2* -m setup -a "gather_subset=network" | grep address  # on rhhost1
ifconfig | grep inet  # on rhhost2`

Dump setup information into a file:

bashCopy code

`ansible rhhost2* -m setup --tree /tmp/facts
mv /tmp/facts/rhhost2.localnet.com /tmp/facts/rhhost2.localnet.com.json`

Visualize with Firefox.

### 5\. Ansible Console

bashCopy code

`ansible -b -K
cd webservers`

List inventory:

bashCopy code

`ansible-inventory --list --output inventory.json`

III. Creating a Simple Ansible Playbook: apache.yml
---------------------------------------------------

yamlCopy code

`---
- name: Apache server installed
  hosts: webservers
  become: yes
  tasks:
    - name: Latest apache version installed
      yum:
        name: httpd
        state: latest
    - name: Apache enabled and running
      service:
        name: httpd
        enabled: true
        state: started`

### Downloading Atom Editor

bashCopy code

`sudo vi /etc/yum.repos.d/atom.repo
# Add Atom repo configuration
sudo yum install -y Atom`

### Configuring VIM for YAML

Create `~/.vimrc`:

bashCopy code

`" YAML config
au! BufNewFile,BufReadPost *.{yaml,yml} set filetype=yaml foldmethod=indent
autocmd FileType yaml setlocal ts=2 sts=2 sw=2 expandtab `

Install Vim indentLine plugin:

bashCopy code

`git clone https://github.com/yggdroot/indentLine.git ~/.vim/pack/vendor/start/indentLine`

Install YAML lint:

bashCopy code

`sudo yum install -y yamllint`

Relax the config:

bashCopy code

`mkdir ~/.config/yamllint
sudo vi ~/.config/yamllint/config
# Add yamllint config`

### Experimenting with Playbook

yamlCopy code

`---
- hosts: webservers
  tasks:
    - name: Ansible facts
      debug:
        msg: "{{ ansible_facts['enp0s8']['ipv4']['address'] }}"`

### Looping with List

Modify `roles/base/tasks/main.yml`:

yamlCopy code

`---
- name: Install base packages
  yum: "name={{ item }} state=present"
  loop: "{{ base_packages }}"
- name: Start the firewalld service
  service: name=firewalld state=started enabled=yes`

Create variables in `group_vars/all`:

yamlCopy code

`# Variables for the all group
base_packages:
  - python3-libselinux
  - python3-libsemanage
  - firewalld`

### Using Jinja2

Modify `welcome.yml` and `welcome.j2`:

yamlCopy code

`# welcome.yml
---
- hosts: all
  vars:
    Address: '192.168.3.110'
    Name: 'user1'
  tasks:
    - name: Ansible Template Example
      template:
        src: welcome.j2
        dest: /home/user1/welcome.txt`

jinja2Copy code

`# welcome.j2
-------------
Hello {{ Name }}, your address is {{ Address }}.
Welcome`

Check the welcome.txt file on `rhhost2`.
