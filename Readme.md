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
  - Dependency Role: [tersmitten.postfix](https://galaxy.ansible.com/tersmitten/postfix)
  - Installs Postfix and copies over custom templates from the `templates` folder.


## Vagrant Quickstart

Start the Vagrant box:

    vagrant up

This starts up a local Vagrant box listening on `192.168.33.10`.
Please see `Vagrantfile` for more details.


Install the Ansible requirements:

    ansible-galaxy install -f -r requirements.yml

This will download all the required roles for this Playbook into the `roles`
directory.


Run the Vagrant playbook:

    ansible-playbook -i vagrant_hosts.cfg vagrant_playbook.yml

This will run the Playbook `vagrant_playbook.yml` on the hosts defined in
`vagrant_hosts.cfg`.


Open a browser and go to `https://192.168.33.10` to access your SENAITE site.

There is a control panel website at https://192.168.33.10/control_panel
installed, which contains static links to the other installed services.


## Troubleshooting

This section provides answers and solutions to some common answsers and pitfalls.

<dl>
  <dt>
    I see only a plain Plone website installed.
  </dt>
  <dd>
    You need to go to the add-ons control panel and install SENAITE Core/LIMS, e.g.
    https://192.168.33.10/prefs_install_products_form
  </dd>

  <dt>
    I get this Error running buildout:
    <pre>
    handler in zc.buildout.easy_install.UNPACKERS
    While:
      Installing.
      Loading extensions.
      Getting distribution for 'mr.developer==1.37'.
    Error: Wheels are not supported
    </pre>
  </dt>
  <dd>
    Setuptools 38.2.0 started supporting wheels which fails in zc.buildout < 2.10.0.
    Please pin zc.buildout to version 2.10.0 in your buildout.cfg
  </dd>
</dl>




[1]: https://github.com/senaite/bika.lims "SENAITE"
[2]: https://plone.org "Plone"
[3]: https://galaxy.ansible.com "Ansible Galaxy"
[4]: https://github.com/plone/ansible.plone_server "Plone Server Role"
[5]: https://www.vagrantup.com/downloads.html "Download Vagrant"
