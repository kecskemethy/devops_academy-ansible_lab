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
 [![asciinema - devops academy 01: ansible - tools ready](https://asciinema.org/a/244396.svg)](https://asciinema.org/a/244396)
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
[![asciinema - evops academy 01: ansible - tools ready](https://asciinema.org/a/244408.svg)](https://asciinema.org/a/244408)
 or repo file via player as previously described as [asciinema/244408.json](asciinema/244408.json)

## Ansible control server setup (acs)

In this section we log in to our first virtual server and we set it up as ansible control server.
