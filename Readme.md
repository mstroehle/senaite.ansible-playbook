<div align="center">

  <a href="https://github.com/senaite/senaite.ansible-playbook">
    <img src="static/logo.png" alt="SENAITE" height="64" />
  </a>
  <h1>SENAITE Ansible Playbook</h1>

  <p>This Ansible playbook provisions a complete industry-proven SENAITE server</p>
</div>


## Introduction

This paybook provisions a [SENAITE][1] installation running on a suitable
[Plone][2] environment.

The Playbook is splitted into different tasks which need to be run in a specific order:

- `senaite_base.yml`
  - Installs system packages.

- `senaite_plone.yml`
  - Dependency Role: [plone.plone_server](https://github.com/plone/ansible.plone_server)
  - Installs: Plone, Supervisor

- `senaite_haproxy`
  - Dependency Role: [geerlingguy.haproxy](https://galaxy.ansible.com/geerlingguy/haproxy)
  - Installs HAProxy and copies over custom templates from the `templates` folder.

- `senaite_varnish`
  - Dependency Role: [geerlingguy.varnish](https://galaxy.ansible.com/geerlingguy/varnish)
  - Installs Varnish and copies over custom templates from the `templates` folder.

- `senaite_nginx`
  - Dependency Role: [geerlingguy.nginx](https://galaxy.ansible.com/geerlingguy/nginx)
  - Installs NGINX and copies over custom templates from the `templates` folder.

- `senaite_munin`
  - Dependency Role: [geerlingguy.munin](https://galaxy.ansible.com/geerlingguy/munin)
  - Installs MUNIN and copies over custom templates from the `templates` folder.

- `senaite_postfix`
  - Dependency Role: [tersmitten.postfix](https://galaxy.ansible.com/oefenweb/postfix)
  - Installs Postfix and copies over custom templates from the `templates` folder.


## Vagrant Quickstart

Start the Vagrant box:

    vagrant up

<p align="center">
	<img src="https://cdn.rawgit.com/senaite/senaite.ansible-playbook/master/static/vagrant-up.svg" alt="Terminal output when running vagrant-up" />
</p>

This starts up a local Vagrant box listening on `192.168.33.10`.
Please see `Vagrantfile` for more details.


Install the Ansible requirements:

    ansible-galaxy install -f -r requirements.yml

<p align="center">
	<img src="https://cdn.rawgit.com/senaite/senaite.ansible-playbook/master/static/ansible-galaxy.svg" alt="Terminal output when running ansible-galaxy" />
</p>

This will download all the required roles for this Playbook into the `roles`
directory.


Run the Vagrant playbook:

    ansible-playbook -i vagrant_hosts.cfg vagrant_playbook.yml


<p align="center">
	<img src="https://cdn.rawgit.com/senaite/senaite.ansible-playbook/master/static/ansible-playbook.svg" alt="Terminal output when running ansible-playbook" />
</p>

This will run the Playbook `vagrant_playbook.yml` on the hosts defined in
`vagrant_hosts.cfg`.


Open a browser and go to `https://192.168.33.10` to access your SENAITE site.

There is a control panel website at https://192.168.33.10/control_panel
installed, which contains static links to the other installed services.

## Troubleshooting

This section provides answers and solutions to some common answsers and pitfalls.

### I see only a plain Plone website installed

You need to go to the add-ons control panel and install SENAITE Core/LIMS, e.g.
https://192.168.33.10/prefs_install_products_form

### Errors occur during buildout


#### Error: Wheels are not supported

Traceback:

```
handler in zc.buildout.easy_install.UNPACKERS
While:
  Installing.
  Loading extensions.
  Getting distribution for 'mr.developer==1.37'.
Error: Wheels are not supported
```

Setuptools `38.2.0` started supporting wheels which fails in `zc.buildout < 2.10.0`.
Please pin `zc.buildout` to version `2.10.0` in your buildout.cfg


#### Error: Couldn't find a distribution for 'plone.api'

Please add this index section to your `buildout.cfg`:

```
[buildout]
...
index = https://pypi.python.org/simple/
...
```


## Customizing

Create a folder, e.g. `my.senaite.playbook` and clone the
`senaite.ansible-playbook` into it:

    mkdir my.senaite.playbook
    cd my.senaite.playbook
    git clone git@github.com:senaite/senaite.ansible-playbook.git
    touch ansible.cfg configure.yml hosts.cfg playbook.yml


The final file structure should look like this:

    my.senaite.playbook
    ├── ansible.cfg
    ├── configure.yml
    ├── hosts.cfg
    ├── playbook.yml
    └── senaite.ansible-playbook


In the `ansible.cfg` you need to enter where ansible should look for roles:

    vim ansible.cfg

    [defaults]
    roles_path = senaite.ansible-playbook/roles


In the `configure.yml` you can overwrite all variables from
`senaite.ansible-playbook/senaite_configure.yml`:

    vim configure.yml

    ---
    plone_initial_password: "mysecret"
    munin_admin_user: "admin"
    munin_admin_password: "mysecret"
    haproxy_stats_user: "admin"
    haproxy_stats_pass: "mysecret"
    supervisor_http_user: "admin"
    supervisor_http_pass: "mysecret"
    firewall_open_tcp_ports: [22, 25, 80, 443]
    nginx_keepalive_timeout: "300"


The `hosts.cfg` defines your custom host:

    vim hosts.cfg

    [vm]
    senaite ansible_ssh_host=172.16.65.100 ansible_ssh_user=myuser ansible_ssh_private_key_file=~/.ssh/id_rsa


Finally the `playbook.yml` puts all the pieces togehter:

    vim playbook.yml

    ---

    - hosts: vm
      become: yes
      become_method: sudo
      gather_facts: yes
      pre_tasks:
        - include: senaite.ansible-playbook/senaite_pre_tasks.yml
        # Include our onw overwrite variables
        - name: Include variables defined in `configure.yml`
          include_vars: configure.yml
          tags:
            - always

      tasks:
        - include: senaite.ansible-playbook/senaite_base.yml
        - include: senaite.ansible-playbook/senaite_plone.yml
        - include: senaite.ansible-playbook/senaite_haproxy.yml
        - include: senaite.ansible-playbook/senaite_varnish.yml
        - include: senaite.ansible-playbook/senaite_nginx.yml
        - include: senaite.ansible-playbook/senaite_munin.yml
        - include: senaite.ansible-playbook/senaite_postfix.yml
        - include: senaite.ansible-playbook/senaite_security.yml

      handlers:
        - include: senaite.ansible-playbook/senaite_handlers.yml

Now you can run your playbook with this command:

    ansible-playbook -i hosts.cfg playbook.yml


[1]: https://github.com/senaite/bika.lims "SENAITE"
[2]: https://plone.org "Plone"
[3]: https://galaxy.ansible.com "Ansible Galaxy"
[4]: https://github.com/plone/ansible.plone_server "Plone Server Role"
[5]: https://www.vagrantup.com/downloads.html "Download Vagrant"
