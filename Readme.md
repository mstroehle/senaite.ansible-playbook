<div align="center">

  <a href="https://github.com/senaite/senaite.ansible-playbook">
    <img src="static/logo.png" alt="SENAITE" height="64" />
  </a>
  <h1>SENAITE Ansible Playbook</h1>

  <p>This package provides a complete Ansible Playbook for SENAITE</p>
</div>


This paybook provisions a [SENAITE][1] installation running on a suitable
[Plone][2] environment.


## Vagrant

Vagrant is a tool for building and managing virtual machine environments in a single workflow.

This playbook contains a fully working [Vagrant][5] setup to install this role.

Please follow these instructions to install Vagrant:
https://www.vagrantup.com/docs/installation/

Mac users can also follow these instructions:
http://sourabhbajaj.com/mac-setup/Vagrant/README.html

Please ensure you have a recent version of [Vagrant][5] installed on your machine.

### Vagrant Commands

Please see here for a list of available Vagrant commands:
https://www.vagrantup.com/docs/cli/

Start the Vagrant machine:

    vagrant up

https://www.vagrantup.com/docs/cli/up.html


Access the Vagrant machine:

    vagrant ssh

https://www.vagrantup.com/docs/cli/ssh.html

Stop the Vagrant box:

    vagrant halt

https://www.vagrantup.com/docs/cli/halt.html


## Dependecies

This role depends on the [Plone Server Role][3].


[1]: https://github.com/senaite/bika.lims "SENAITE"
[2]: https://plone.org "Plone"
[3]: https://galaxy.ansible.com "Ansible Galaxy"
[4]: https://github.com/plone/ansible.plone_server "Plone Server Role"
[5]: https://www.vagrantup.com/downloads.html "Download Vagrant"
