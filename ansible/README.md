Building a simple LAMP stack and deploying Application using Ansible Playbooks.
-------------------------------------------
Borrowed from https://github.com/ansible/ansible-examples/tree/master/lamp_simple
but modified some of the roles to work on ubuntu/debian based systems.

These playbooks require Ansible 2.0+. I've tested it with 2.5.1

These playbooks are meant to be a reference and starter's guide to building
Ansible Playbooks.

This LAMP stack can be on a single node or multiple nodes. The inventory file
'hosts' defines the nodes in which the stacks should be configured.

        ```bash
        [webservers]
        web

        [dbservers]
        db
        ```

So this stack could be used from any directory but I recommend to copy it to /etc/ansible patch the hosts and ansible.cfg files there.
The stack can be deployed using the following command:

        ```bash
        sudo cp -r group_vars roles *.patch site.yml /etc/ansible
        cd /etc/ansible
        patch < hosts.patch
        patch < ansible.cfg.patch
        ansible-playbook site.yml
        ```

Once done, you can check the results by browsing to http://localhost:8080/ from the deployment windows box where your development env runs in virtualbox.
(Vagrant configured to port forward web server 80 port to localhost 8080)
