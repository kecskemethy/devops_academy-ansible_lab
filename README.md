# DevOPs Academy 2019 - Ansible Lab

This is a hands on introduction to Ansible.

## Tools we use
* vagrant - to set up virtual machines where we can use Ansible for configuration management
* virtualbox - vagrant is going to create vurtual machines using virtualbox (we could use docker or any other virtualization technology as well)

### Installation

This LAB expects you are working on Windows 10. Of course these tools works fine in Linux as well.

 - go to https://www.vagrantup.com/downloads.html and get vagrant 64bit for your OS.
 - same for Virtualbox https://www.virtualbox.org/wiki/Downloads

Install them both and make sure their binaries are in PATH.

So if you could test them like e.g.
 [![asciicast](https://asciinema.org/a/244396.svg)](https://asciinema.org/a/244396)
 ascii cast file saved in repo [asciinema/244396.json](asciinema/244396.json) - in case above got archived - you can view with `asciinema play asciinema/244396.json`
 if you need asciiname for windows you can install it by following this [build tutorial](https://asciinema.org/a/244387)
 you need to install golang for it first, use msi installer for windows!

## Vagrant env setup

in [vagrant](vagrant/) sub-folder of this repository there is a prepared [Vagrantfile.lab](vagrant/Vagrantfile.lab) for you to help you to get 3 virtual machines up for this lab.

I also recorded a screen cast of this setup so you can watch these few commands how executes and what kind of output to expect.
Be aware: It could take much longer for you as vagrant going to download virtual machine binaries for each system(~1.8G).

### vagrant setup commands
- `vagrant init`
- edit/replace created `Vagrantfile`
- `vagrant up` # to start up the virtual servers!
- test via SSH: `vagrant ssh acs`

See ascii cast remotely at:
[![asciicast](https://asciinema.org/a/244408.svg)](https://asciinema.org/a/244408)
 or repo file via player as previously described as [asciinema/244408.json](asciinema/244408.json)

## Ansible control server setup (acs)

In this section we log in to our first virtual server and we set it up as ansible control server.

You have learned that ansible uses SSH for communication between servers. Modern distributions have *SSH password based authentication off* by default for security reasons. So we need to setup SSH key based access between our servers.

Steps:
1. login to acs and add two lines into hosts file (this step is optional as we could refer servers by IP but convinient)
1. login to acs and generate a key we  use for authenticaion, copy public key to clipboard
1. login to web and db server and add pub key to .ssh/authorized_keys
1. login back to acs and test if our passwordless / key based connection works between our servers

- To refer OS level other virutal servers in name we can add them into  /etc/hosts
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
- gen SSH key in control server and add it into web and test
    ```bash
    vagrant ssh acs
    ssh-keygen -b 4096
    tree .ssh/
    cat .ssh/id_rsa.pub
    exit
    vagrant ssh web
    sudo yum install vim
    vim .ssh/authorized_keys
    exit
    vagrant ssh acs
    ssh web
    exit
    exit
    ```
    screen cast of above two in one
    [![asciicast](https://asciinema.org/a/244464.svg)](https://asciinema.org/a/244464)
- finally ansible setup in acs
    ```bash
    vagrant ssh acs
    sudo su -
    apt-get update
    apt-cache search ansible
    apt-get install ansible
    cd /etc/ansible
    cat <<EOT > ansible.cfg.patch
    --- ansible.cfg 2019-05-05 10:08:28.001986218 +0000
    +++ ansible.cfg.ok      2019-05-05 10:08:16.197986218 +0000
    @@ -59,7 +59,7 @@
    #roles_path    = /etc/ansible/roles

    # uncomment this to disable SSH key host checking
    -#host_key_checking = False
    +host_key_checking = False

    # change the default callback, you can only have one 'stdout' type  enabled at a time.
    #stdout_callback = skippy
    EOT
    cat <<EOT > hosts.patch
    --- hosts       2019-05-05 10:02:16.769986218 +0000
    +++ hosts.ok    2019-05-05 09:59:41.389986218 +0000
    @@ -14,14 +14,16 @@
    #blue.example.com
    #192.168.100.1
    #192.168.100.10
    +acs ansible_connection=local

    # Ex 2: A collection of hosts belonging to the 'webservers' group

    -#[webservers]
    +[webservers]
    #alpha.example.org
    #beta.example.org
    #192.168.1.100
    #192.168.1.110
    +web ansible_host=192.168.33.20

    # If you have multiple hosts following a pattern you can specify
    # them like this:
    @@ -30,12 +32,13 @@

    # Ex 3: A collection of database servers in the 'dbservers' group

    -#[dbservers]
    +[dbservers]
    #
    #db01.intranet.mydomain.net
    #db02.intranet.mydomain.net
    #10.25.1.56
    #10.25.1.57
    +db ansible_host=192.168.33.30

    # Here's another example of host ranges, this time there are no
    # leading 0s:
    EOT
    patch < hosts.patch
    cat ansible.cfg.patch
    exit
    ansible all -m ping
    ```

- a few more command line options to try for ansible.

```bash
ansible all -m ping -vvv
ansible web -m command -a "cat /etc/os-release"
ansible all -m command -a "cat /etc/os-release"
ansible db -m command -a "cat /etc/debian_version"
ansible web -m command -a "sudo yum -y install mc"
ansible web -a "sudo yum -y install mc"
ansible webservers -m yum -a "name=httpd state=present" --sudo
ansible db -m user -a "name=dbuser password=dbuserpwd" --sudo
ansible web -m setup
ansible web -m setup -a "filter=ansible_eth*"
```
