# Ansible-Project

Project to manage 2 host machines via a controller machine and install softwares on them by running playbooks.

Requirements:
VirtualBox, Vagrant, GitBash, VScode (or any text editor)

steps to create VM:
1. Open GitBash and navigate where we want to setup Ansible Project.
2. Create a new directory for Ansible controller VM by running the command mkdir ansible-controller.
3. Navigate inside ansible-controller and create a new file called vagrantfile by running vagrant init centos/7.
4. Edit the vagrantfile to provision Ansible on VM.
5. Save and run vagrant validate and thenm vagrant run, to make VM up asnd running.
6. Connect to it using SSH by running vagrant ssh and checkl version of Ansible there.
7. Create a new directory for ansible-project.
8. Navigate inside and create new file called inventory by running command touch inventory.
9. Create new file called myPlaybook.yml, as of now keep it empty.
10. Run command ssh-keygen to generate SSH key pair. go to ~/.ssh folder and check public and private keys generated and keep public key copied copied.
11. create two more VM named webserver and dbserver inside a new folder ansible-hosts.
12. Make sure all the machines are up and running.
13. Run command ip addr on each maschine and check there ips and havingf ip in same range.
14. Copy the public key of ansible-controller to host machines by running the command ssh-copy-id <user>@<host>
15. test connection by running the ssh command with ip address of host machines. ssh vagrant@192.168.-.-

Note: we can also manually copy the contents of .pub file and paste it into ~/.ssh/authorized_keys file of host-machines.

Adding inventory and playbook file on controller and run playbook:
1. connect ton ansible-controller using SSH, vagrant ssh <machine name>
2. Edit the inventory file and add ip address or hostnames of host machines we want to control.
    [webservers]
    192.168.33.10

    [dbservers]
    192.168.33.11
3. run command ansible all -m ping -i inventory to test connection to host-machines.
4. edit the myPlaybook.yml file and add instaructions for host machines.
5. run playbook on managed hosts by running the command ansible-playbook -i inventory myPlaybook.yml
6. once playbook has run, we can check the webserver by opening a web browser and navigating to that ip of webserver.


myPlaybook.yml
{
---
- name: Install and Start Web Servers
  hosts: all
  become: true
  tasks:
    - name: Install Apache web server
      yum:
        name: httpd
        state: present
      when: "'webserver' in group_names"

    - name: Start Apache web server
      service:
        name: httpd
        state: started
      when: "'webserver' in group_names"

    - name: Install Nginx
      yum:
        name: nginx
        state: present
      when: "'dbserver' in group_names"

    - name: Start Nginx
      service:
        name: nginx
        state: started
      when: "'dbserver' in group_names"


}