# django-vagrant-ansible
Skeleton repository for Django 1.10 projects developed under Vagrant and provisioned with Ansible.

Stack:
* Python 3.5
* PostgreSQL 9.5
* PostGIS 2.2
* Django 1.10.5
* Nginx with uwsgi

Environment will include two virtual machines:
* `db` - hosting PostgreSQL database
* `app` - hosting Django app

# Requirements
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Vagrant](https://www.vagrantup.com/)
* [Ansible](https://www.ansible.com/)

# Prepare the environment
* clone the repository to a desided location on your disk and `cd` to it
* `vagrant up`
* `ssh-add .vagrant/machines/app/virtualbox/private_key`
* `ssh-add .vagrant/machines/db/virtualbox/private_key`

#  Install required Ansible packages
`ansible-galaxy install ANXS.postgresql`
`ansible-galaxy install lborguetti.system-locale`

# Provision the virtual machines with Ansible
* `cd server_config`
* `ansible-playbook -i inventory.ini main.yml -l db_dev -vvvv`
* `ansible-playbook -i inventory.ini main.yml -l app_dev -vvvv`

# That's it
Basic skeleton development environment has been set up.
See the next section for optional next steps.

# Next steps
You might want to continue with setting up django environment now:

* connect to your virtual machine: `vagrant ssh app`
* cd to the project and activate the virtual environment: `cd /vagrant && . bin/activate`
* `django-admin startproject django_vagrant_ansible`
* open the `settings.py` and update:
  * the `DATABASES` dictionary with the credentials for the PosgreSQL DB (`server_config/vars/db.yml`)
  * `ALLOWED_HOSTS` to `['192.168.103.100',]`
  * all other settings which are necessary
* `python manage.py migrate`
* visit http://192.168.103.100:8000 in your browser


## Possible issues

### "unreachable=1", "Failed to connect to the host via ssh", "Connection timed out"
This usually happens where you create multiple vagrant instances, the solution should be easy:
* run `ssh-add -l`

to list all identities and confirm that there are more than one identities for vagrant `db` and `app` virtual machines.
If this is the case:
* run `ssh-add -D` to remove all identities from the agent
* change directory to the root of the project
* `ssh-add .vagrant/machines/db/virtualbox/private_key` (re-add `db` private key)
* `ssh-add .vagrant/machines/app/virtualbox/private_key` (re-add `app` private key)
* you probably want to run `ssh-add` as well to re-add your personal identity

### "No package match libgeos-c1 is available" when provisioning `db` virtual machine

* edit `/etc/ansible/roles/ANXS.postgresql/tasks/extensions/postgis.yml` file (sudo)
* replace `- libgeos-c1` with `- libgeos-c1v5`
* replace `- "postgresql-{{postgresql_version}}-postgis-{{postgresql_ext_postgis_version}}-scripts"` with `- "postgresql-{{postgresql_version}}-postgis-scripts"`

see: https://github.com/ANXS/postgresql/issues/159