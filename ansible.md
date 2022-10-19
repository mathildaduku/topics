Ansible is a configuration management software. 
It is a lazy way for software engineers particluarly cloud engineers to carry out multiple tasks on different servers at once. With ansible you can set multiple configurations and deploy apps on multiple servers all at once from one control server.

Ansible is agentless. You dont need any third party to connect between the servers, all you need is an SSH connection and a control node or host or system. The control node carries out all the tasks, it just needs to connect with all the various servers and voila it can execute whatever task you need.

Essentials in ansible
1. Your hosts: Here you need two hosts or more depending on you and the company. One host MUST be a control host, the other(s) would be the target host(s).
Control host: As the name implies this is the host that controls everything, this is where all the magic happens. Ansible and its dependencies(e.g python) is installed here. The playbook and inventory file lives here also (dont worry we'll talk more about it later). From here whatever configuration you want to deploy on all your servers, you do it from here. It sends out all the instructions
Target host(s): These guys recieve all the magic. Most times nothing needs to be installed here, they just sit pretty and let the control host do all the work. Whatever instructions the control host demands, it executes. Its on the recieving end, the control host pushes everything to this side. Hence, why it is said that ansible has a "push model". 

2. Inventory: I don't know about you but when I hear the word "inventory", you think of a list, record, catalog. Well in this scenario it also applies. Inventory is a list of the target host(s). This is a file where you list out all the target host you want to control. Although this is a file that stores target hosts, it is located on the control server. Wait, you thought it was going to be on the target server? How will the control server know who it is controlling? So you see it has to be on the control server. 
Now on this inventory file, the targets can be represented by their ip address or their domain name. Now if youre lazy like software engineers, you might want to group your target servers especially if youre dealing with a lot of them (e.g 50 web servers, 20 database servers, 70 cloud servers all at once), this will make it easier for you, trust me. So if you have like 50 web servers you put [web servers] on top of the list of web servers, so if you want to make changes on only the web servers you can easily access them because of that grouping [] you did.

3. Playbook: just like the name implies, this is where the fun begins. Its a YAML (Yet another markup language) file i.e it is written in YAML language. This file is located in the control host server. This is where you write out the commands that will be executed in the target hosts. Each command is called a play and a combination of multiple commands (plays) is called a playbook. What is inside this playbook?
i) a list of all the target hosts by their ip address or domain name. You can also specify the hosts by their group name to save time or you can use "all".
ii) the user thats taking responsibilities for all these, could be root so user: root or you can take a sudo role become: sudo
iii) a play: which consists of the name of what youre doing and the tasks that will make it possible

4. Modules: To make life more meaningful for you, modules are made to save the day. Whatever task you want ot acheive best believe theres a module that shows you how to write out for best results. Its like a template for whatever task you want to acheive with ansible.

5. Roles: These are premade ansible playbooks that people like you and I made so you can easily copy, refine or modify to suit your needs. You want to make your own for people to see? or you want to find others to copy? then GALAXIES would be useful for you, its like an app store where you can upload your playbooks and also download playbooks done by others.

Now you know pretty much everything about ansible except how to actually use and apply. Now lets go:

A) SSH

1. Create your control and target hosts:
vagrant init ubuntu/focal64 (if you're creating a new vagrant environment)
**also need to edit config.vm.boxes make cpu=2, something about processors and saving laptop memory 1024mb**

vagrant up 
vagrant ssh

2. Generate SSH keys in control host:
ssh key-gen

3. Copy ssh key(public) to all the target hosts:
ssh copyid-rsa

4. something about modifying configuration file of machines to allow passwordless entry then going back to disable it.

5. Ssh into the targets as you please

B) Ansible

NOTE:
!) Its advisable to create a separate user with sudo privileges to do all things ansible
!!) You have no business with the target hosts at this point, all the action is happening at the control host

1. Create ansible folder
mkdir ansible

2. Create inventory file
touch inventory

"inventory" could be any name of your choice

3. Write out all the hosts in a list-like form in the inventory file and leave
nano inventory

192.18.321.32
192.73.849.29
192.983.123.39

4. Create playbook file
touch playbook.yml

"playbook" could be any name of your choice

5.Enter playbook file and write put hosts, plays and tasks with their modules
nano playbook.yml

Hosts
192.18.321.32
192.73.849.29
192.983.123.39

Name: Install Apache 

 tasks:
- name: Install apache
- apt: Apache2
- status: Update

- name: copy php file
 - copy:
 - src: /home/vagrant/ansible/index.php
 - dest: /www/var/html/

6. Test run your ansible software

ansible-playbook playbook.yml -i inventory --check
-i points to where the inventory host file is located if not added, itll automatically point to the default host file created by the machine

7. If everything is ok, no syntax error, everything runs fine then you can proceed to ACTUALLY running it

ansible-playbook playbook.yml -i inventory 

8. Use ip address of target host on your browser and see if it shows apache page it works. Add /index.php to see if it'll show date and time

9. Run systemctl status apache on target hosts to see if apache is running successfully you can even see how long the server has been up
