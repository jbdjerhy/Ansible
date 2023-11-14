# Ansible Automation Project Documentation

========================================

### 0\. Initial Environment

![Picture1](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/6773c56b-48f8-49c9-bcc1-fe86b2d28f7b)

---------------------

### 1\. Installing Required Dependencies

bash*

`sudo yum install -y epel-release

sudo yum install -y python3

sudo alternatives --set python /usr/bin/python3`

### 2\. Installing Ansible

bash*

`sudo yum install -y ansible`

![Picture2](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/a50ca598-f6d5-4afc-a2e2-2881e4127bc5)

Installed Ansible Core version: 2.14.2

### 3\. Installing Argcomplete for Shell Completion

bash*

`sudo yum install -y python3-argcomplete

sudo activate-global-python-argcomplete`

### 4\. Adding `rhhost2` to Inventory

Edit `/etc/ansible/hosts` and add `rhhost2` to the inventory.

Check with:

bash*

`sudo ansible all --list-hosts`

![Picture3](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/c8d35be3-5b6b-4fa2-a08d-b00320cdff90)

### 5\. Generating SSH Keys

bash*

`sudo ssh-keygen

ls ~/.ssh/`

Copy keys to `rhhost2`:

bash*

`ssh-copy-id 10.0.2.22`

Check success:

bash*

`ansible -m ping all`

### 6\. Sending Uptime Command

bash*

`ansible -a "uptime" all`

![Picture4](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/596fda17-ad52-4bf8-b363-7148b0683c1e)

### 7\. Modifying Ansible Hosts File

Edit `/etc/ansible/hosts` to add `rhhost2` to the `webserver` group.

Try ad hoc commands.

II. Using Ad Hoc Commands

![Picture5](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/de33933d-ca78-4de5-a015-9223b1d2fb20)

![Picture6](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/96fe81ac-10d8-4bac-ab3e-9df082ac81a6)

-------------------------

### 1\. Creating Files with 'file' Module

bash*

`ansible rhhost2 -m file -a "path=/home/user1/file2.txt state=touch mode=700"

![Picture7](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/17913302-7445-43ff-8256-c160096b9b1c)

ansible rhhost2 -m file -a "path=/home/user1/file3.txt state=touch mode=700"

![Picture8](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/61fab0d2-7a87-47aa-8b7e-d8691c088e1e)

ansible rhhost2* -m file -a "dest=/home/user1/file2.txt mode=600 owner=root group=root" -b -K`

![Picture9](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/26a5ab77-caf5-422b-be66-cadc5511659e)


[user1@rhhost1 ~]$ ansible rhhost2* -m file -a "dest=/home/user1/newdir mode=755 state=directory"

![Picture10](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/8caeafa1-1aab-4407-88dd-9f5286dd561a)

### 2\. Installing HTTPD with Yum Module

bash*

`ansible rhhost2* -m yum -a "name=httpd state=installed" -b -K

ansible rhhost2* -m service -a "name=httpd state=started" -b -K

ansible rhhost2* -m service -a "name=httpd state=reloaded" -b -K

ansible rhhost2* -m service -a "name=httpd state=enabled" -b -K

ansible rhhost2* -m service -a "name=httpd enabled=no" -b -K --check

![Picture11](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/4415208b-4c6f-48af-9fa5-000730b25aa1)

ansible rhhost2* -m shell -a "systemctl status httpd"

![Picture12](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/75ecab15-ff8d-4532-97f6-86d715992f7b)

ansible rhhost2* -m shell -a "/sbin/reboot" -b -K`

### 3\. Managing Users

bash*

`ansible rhhost2* -m user -a 'name=user2 state=present home=/home/user2 shell=/bin/bash' -b -K

ansible rhhost2* -m user -a 'name=user2 group=user2 groups=wheel' -b -K

![Picture13](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/a8288784-9496-4cb5-b5b8-3b378942098a)

ansible rhhost2* -m user -a 'name=user2 state=absent' -b -K`

![Picture14](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/2ce49de5-24db-45ee-8862-de2b338ad951)

### 4\. Gathering Host Data

bash*

`ansible rhhost2* -m setup -a "gather_subset=network" | grep address  # on rhhost1

ifconfig | grep inet  # on rhhost2`

![Picture15](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/7e98619d-a261-4b73-931c-b6fd81a68d4d)

Dump setup information into a file:

bash*

`ansible rhhost2* -m setup --tree /tmp/facts

mv /tmp/facts/rhhost2.localnet.com /tmp/facts/rhhost2.localnet.com.json`

Visualize with Firefox.

![Picture16](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/ac4eae46-4850-4dbd-9f04-eea847df4943)

### 5\. Ansible Console

![Picture17](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/f02e5b9f-3327-4bb7-b385-d10f515e7169)

bash*

`ansible -b -K

cd webservers`

List inventory:

bash*

`ansible-inventory --list --output inventory.json`

![Picture18](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/a6d672f9-369b-4272-a79d-f2a9e0691f0e)


III. Creating a Simple Ansible Playbook: apache.yml

---------------------------------------------------

yaml*

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

![Picture19](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/f9d9a9c8-20ec-4021-b190-fb690667d740)

### Downloading Atom Editor

bash*

`sudo vi /etc/yum.repos.d/atom.repo


### Add Atom repo configuration

sudo yum install -y Atom`


[Atom]

name=Atom Editor

baseurl=https://packagecloud.io/AtomEditor/atom/el/7/$basearch

enabled=1

gpgcheck=0

repo_gpgcheck=1

gpgkey=https://packagecloud.io/AtomEditor/atom/gpgkey

### Configuring VIM for YAML

Create `~/.vimrc`:

bash*

`" YAML config

au! BufNewFile,BufReadPost *.{yaml,yml} set filetype=yaml foldmethod=indent

autocmd FileType yaml setlocal ts=2 sts=2 sw=2 expandtab `

Install Vim indentLine plugin:

bash*

`git clone https://github.com/yggdroot/indentLine.git ~/.vim/pack/vendor/start/indentLine`

Install YAML lint:

bash*

`sudo yum install -y yamllint`

Relax the config:

bash*

`mkdir ~/.config/yamllint

sudo vi ~/.config/yamllint/config

### Add yamllint config`
Rules:

  line-length: disable
  
  trailing-spaces: disable

![Picture20](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/1e5e15dd-b3fa-4428-8b20-ffc863814e17)  
  
### Experimenting with Playbook

Experimenting with the following apache.yml playbook: 

 
 ![image](https://github.com/jbdjerhy/Ansible/assets/142699688/54df67df-ef53-4786-819f-a4205be83cb8)

To verify playbooks ahead of executing them I have installed ansible.lint via pip:

sudo yum install -y python3-pip (to make sure we have pip)

Then: pip3 install --user ansible-lint

Check the playbook with ansible-playbook --check apache.yml

Creating hosts, variables and roles files for more structured configurations.
~

![Picture21](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/2e6518f9-d8ef-43c0-8538-515115b0a799)

Using a working role with playbook site.yml:

---

### This playbook deploys all site configs

- name: apply base configuration to all nodes

  hosts: all

  remote_user: root

  - base

Creating the 'base' role:

Vim roles/base/tasks/main.yml

---

### This playbook contains base plays for all nodes

- name: Install firewalld

  yum: name=firewalld state=present

- name: Start the firewalld service

  service: name=firewalld state=started enabled=yes

Now I have :

1.  My static invotory file : hosts
2.  The playbook site.yml
3.  Base role main.yml

Running the playbook:

![Picture22](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/9ec7e8ac-b934-4ec3-b242-68d24a95e0b2)

In order to test the use of the include statement I have:

1)Added the include install_apache.yml in the main.yml

2)Created the install_apache.yml

---

### Install apache

- name: Install apache

  yum: name=httpd state=present

- name: Apache service state

  service: name=httpd state=started enabled=yes

- name: Start firewalld

  service: name=firewalld state=started enabled=yes

- name: Add firewall rule for apache

  firewalld: port=80/tchp permanent=true state=enabled immediate=yes

1.  And modified the site.yml as follows:

---

### This playbook deploys all site configs

- name: apply base configuration to all nodes

  hosts: all

  remote_user: root

  roles:

  - base

- name: Configure webservers

  hosts: webservers

  remote_user: root

  roles:

  - webservers

Now a rerun of the site.yml to test the include statement:

![Picture23](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/473dc702-87b0-48c9-a174-7a532cd0a2a6)

~

Using variables:

Creating the webserver file in /ansible-files/group_vars

Setting the var http_port: 80

Replacing the port value in the install_apache.yml with the vairalbe and retesting the playbook

And it runs successfully.

Experimenting with ansible facts:

Created the yaml file : ansible-facts.yml

---

- hosts: webservers

  tasks:

    - name: Ansible facts

      debug:

        msg: "{{ ansible_facts['enp0s8']['ipv4']['address'] }}"

Ran successfully ans returned IP informaotion for enp0s8

![Picture24](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/8d85b886-9851-4549-b7dd-1557d4327fd7)

### Looping with List

Modify `roles/base/tasks/main.yml`:

yaml*

`---

- name: Install base packages

  yum: "name={{ item }} state=present"

  loop: "{{ base_packages }}"

- name: Start the firewalld service

  service: name=firewalld state=started enabled=yes`

Create variables in `group_vars/all`:

yaml*

`# Variables for the all group

base_packages:

  - python3-libselinux

  - python3-libsemanage

  - firewalld`
  
Ran site.yml successfully

![Picture25](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/844eceb7-8732-4622-926c-4f510b5b2334)


### Using Jinja2

Modify `welcome.yml` and `welcome.j2`:

yaml*

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

jinja2*

`# welcome.j2

-------------

Hello {{ Name }}, your address is {{ Address }}.

Welcome`

Check the welcome.txt file on `rhhost2`.

![Picture26](https://github.com/jbdjerhy/CyberSecurity-Projects/assets/142699688/000df270-184a-46cf-b3da-95db09215b90)
