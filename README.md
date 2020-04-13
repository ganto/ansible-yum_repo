Ansible Role: ganto.yum_repo
============================

This role allows you to do any task on YUM repository files you'll ever need. It can:

- Install new repo files from release packages
- Import public GPG keys for verifying RPM packages
- Create new `.repo` files
- Configure repository options on existing `.repo` files

The default variables will install the EPEL repository from the release RPM. See the examples below how to define variables for other tasks.


Requirements
------------

This role must run with root permissions.


Getting Started
---------------

1. Download this role via:

        $ ansible-galaxy install ganto.yum_repo

2. Create a minimal playbook (e.g. called `epel-repo.yml`) to run this role:

        - name: Setup EPEL repository
          host: localhost
          become: True

          roles:
            - name: ganto.yum_repo

3. Run the playbook:

        $ ansible-playbook epel-repo.yml

        PLAY [Setup EPEL repository] ************************
        ...


After playbook execution `yum` or `dnf` (depending on the Linux distribution) is able to install packages from the EPEL repository.


License
-------

[Apache-2.0](https://tldrlegal.com/license/apache-license-2.0-(apache-2.0))


Author Information
------------------

The [ganto.yum_repo](https://galaxy.ansible.com/ganto/yum_repo) role was written by Reto Gantenbein | [e-mail](mailto:reto.gantenbein@linuxmonk.ch) | [GitHub](https://github.com/ganto)
