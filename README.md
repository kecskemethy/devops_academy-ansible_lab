# DevOPs Academy 2019 - Ansible Lab

This is a hands on introduction lab to Ansible on Windows. I developed and tested this LAB on Windows 10 but all these tools works fine in Linux.

## Overview

We setup 3 virtual servers in virtualbox. One of them will be our Ansible control server and we set up other two servers and even configure our local server from here using Ansile. Two other servers act as web server and database server.

### Tools we use
* vagrant - we us to automate virtual machine creation (in windos host)
* virtualbox - vagrant our virtualization technology we interact with via vagrant (in windows host)
* git - git client to download code from github (both in windows host and ansible control server)
* ansible - on in control server called acs (in asible conrol server only, not in windows host)
* ssh - for communication between servers. passwrodless auth using keys

### Host installation

 - get and install vagrant from https://www.vagrantup.com/downloads.html
 - same for Virtualbox https://www.virtualbox.org/wiki/Downloads

Once done you could test them like e.g.
 [![asciicast](https://asciinema.org/a/244396.svg)](https://asciinema.org/a/244396)
 ascii cast file saved in repo [asciinema/244396.json](asciinema/244396.json) - in case above got archived - you can view with `asciinema play asciinema/244396.json`
 if you need asciiname for windows you can install it by following this [build tutorial](https://asciinema.org/a/244387)
 you need to install golang for it first, use msi installer for windows!

## Vagrant env setup in host

in [vagrant](vagrant/) sub-folder of this repository there is a prepared [Vagrantfile.lab](vagrant/Vagrantfile.lab) for you to help you to get 3 virtual machines up for this lab.

### vagrant setup commands in host

```cmd
git clone https://github.com/kecskemethy/devops_academy-ansible_lab.git
cd devops_academy-ansible_lab\vagrant
vagrant init
copy Vagrantfile.lab Vagrantfile
# start up all 3 virtual servers in Virtualbox. Could take several minutes.
vagrant up
#test via SSH
vagrant ssh acs
```

I also recorded a screen cast of this setup so you can watch these few commands how executes and what kind of output to expect.
Be aware: It could take much longer for you than in the videa as vagrant is going to download virtual machine binaries in the first time (~1.8G).
[![asciicast](https://asciinema.org/a/244408.svg)](https://asciinema.org/a/244408)
 or repo file via player as previously described as [asciinema/244408.json](asciinema/244408.json)

## Ansible control server setup (acs)

In this section we log in to our first virtual server and we set it up as ansible control server (acs).

You have learned that ansible uses SSH for communication between servers. Modern distributions have *SSH password based authentication off* by default for security reasons. So we need to setup SSH key based access between our servers.

Steps:
1. login to acs and add two lines into hosts file (this step is optional as we could refer servers by IP but convinient)
1. login to acs and generate a key we  use for authenticaion, copy public key to clipboard
1. login to web and db server and add pub key to .ssh/authorized_keys
1. login back to acs and test if our passwordless / key based connection works between our servers

### To able refer OS level other virutal servers using their name we add them into /etc/hosts in acs

    ```bash
    vagrant ssh acs
    sudo su -
    cat << EOF >> /etc/hosts

    192.168.33.20 web
    192.168.33.30 db
    EOF
    exit
    ping web
    ^C
    ping db
    ^C
    exit
    ```

### gen SSH key in control server and add it to web and db server. test ssh connection.

    ```bash
    vagrant ssh acs
    ssh-keygen -b 4096
    tree .ssh/
    cat .ssh/id_rsa.pub
    exit
    vagrant ssh web
    sudo yum -y install vim
    vim .ssh/authorized_keys
    exit
    vagrant ssh db
    sudo apt-get -y install vim
    vim .ssh/authorized_keys
    exit
    vagrant ssh acs
    ssh web
    exit
    ssh db
    exit
    exit
    ```

    screen cast of above two in one
    [![asciicast](https://asciinema.org/a/244464.svg)](https://asciinema.org/a/244464)
    dont forget to setup the uuthorized key for db server as well as that part is not recorded.

### Ansible setup in acs

    ```bash
    # ssh to control server
    vagrant ssh acs
    # get ansible code to acs into vagrant user's home
    git clone https://github.com/kecskemethy/devops_academy-ansible_lab.git
    #change to root user
    sudo su -
    apt-get update
    apt-cache search ansible
    apt-get -y install ansible
    cd /home/vagrant/devops_academy-ansible_lab/ansible/
    cp -r group_vars roles *.patch site.yml /etc/ansible
    cd /etc/ansible
    # patch ansible inventory
    patch < hosts.patch
    # patch ansible configuration
    patch < ansible.cfg.patch
    # exit from root shell
    exit
    # test ansible, see if it can work with hosts defined in its inventory /etc/ansible/hosts
    ansible all -m ping
    exit
    ```

    screen cast of above:


## Ansible in command line

Here are a few command line options to try in the control server. This is not the main use case of Ansible but it is always good to know that we can do on-demand modifications on serves using ansible from command line without scripts.

    ```bash
    # login to control server
    vagrant ssh acs
    # test with ping module, -vvv extra debug output
    ansible all -m ping -vvv
    # run commands on servers using command module
    ansible web -m command -a "cat /etc/os-release"
    ansible all -m command -a "cat /etc/os-release"
    ansible db -m command -a "cat /etc/debian_version"
    ansible web -m command -a "sudo yum -y install mc"
    # soft install using command module (this is the default module so we can skip this -m command option)
    ansible web -a "sudo yum -y install mc"
    # install using yum module for redhat based systems (package module could be a better idea to use as it can handle multiple OSs)
    ansible webservers -m yum -a "name=httpd state=present" --sudo
    # create user for server, suer module
    ansible db -m user -a "name=dbuser password=dbuserpwd" --sudo
    # setup module to see ansible variables
    ansible web -m setup
    ansible web -m setup -a "filter=ansible_eth*"
    exit
    ```

## Ansible plays and playbooks

  This is the main use case of ansible. When we write multiple plays and add them into a playbook to apply all to multiple servers.
  The code in a previous step from the reposiotry already copied to /etc/ansible.
  Here take a look either on the control server or in github into the structure and the yaml files.

    ```bash
    vagrant ssh acs
    cd /etc/ansible/
    # check the structure
    tree
    # apply the infra code
    ansible-playbook site.yml
    ```

    screen cast of above:

