Ansible Role: ganto.yum_repo
============================

[![CI](https://github.com/ganto/ansible-yum_repo/workflows/CI/badge.svg?event=push)](https://github.com/ganto/ansible-yum_repo/actions?query=workflow%3ACI)

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


Configuration Examples
----------------------

The following variables can be defined e.g. in your Ansible inventory, via `vars` in the playbook or in a separate `vars.yml` file.

**Set custom options for EPEL repo file:**

To set e.g. a specific `baseurl` (e.g. a local mirror) define it for each repository e.g. in `yum_repo_default_options`:

    yum_repo_default_options:
      epel:
        baseurl: 'http://mirror.example.com/pub/epel/$releasever/Everything/$basearch'
      epel-debuginfo:
        baseurl: 'http://mirror.example.com/pub/epel/$releasever/Everything/$basearch/debug'
      epel-source:
        baseurl: 'http://mirror.example.com/pub/epel/$releasever/Everything/SRPMS'

The EPEL `.repo` file contains multiple repository definitions. For each of them the `baseurl` option is set accordingly.


**Enable 'centosplus' repository on CentOS:**

The `centosplus` repository is disabled by default on fresh CentOS installation. To enable it, run the role with the following variables:

    yum_repo_release_pkg: ''
    yum_repo_gpg_key: ''
    yum_repo_file: /etc/yum.repos.d/CentOS-centosplus.repo
    yum_repo_custom_options:
      centosplus:
        enabled: '1'

In this case the referenced `.repo` file already exists and we only modify or add a single option.


**Setup a new repository from a release RPM:**

The CentOS SIG (Special Interests Group) has a large collection of repositories for different software projects. Their `.repo` files and GPG keys are often bundled in a release RPM. For example the OpenStack repository can be installed and the included testing repository enabled with the following variable definitions:

    yum_repo_release_pkg: centos-release-openstack-stein
    yum_repo_gpg_key: /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-Cloud
    yum_repo_file: /etc/yum.repos.d/CentOS-OpenStack-stein.repo
    yum_repo_custom_options:
      centos-openstack-stein-test:
        enabled: '1'


**Define a new repository without release RPM:**

To define a local repository that doesn't have a release RPM (e.g. a local development package repository) ensure that all necessary repo options are defined in the options variable:

    yum_repo_release_pkg: ''
    yum_repo_gpg_key: ''
    yum_repo_file: /etc/yum.repos.d/custom.repo
    yum_repo_custom_options:
      custom:
        name: 'My custom repository'
        baseurl: 'http://localhost/repos/custom'
        gpgcheck: '0'
        enabled: '1'

The `.repo` file is created from the ground based on the Ansible variable definitions.


Playbook Examples
-----------------

The role must be run once for each `.repo` file that you want to adjust. To avoid repeating multiple role invocations you can use some Ansible magic to loop over a list of files with options. E.g. to set a proxy for some CentOS repositories:

    - name: Set proxy for default CentOS repositories
      include_role:
        name: ganto.yum_repo
      vars:
        yum_repo_release_pkg: ''
        yum_repo_gpg_key: ''
        yum_repo_file: '/etc/yum.repos.d/{{ yum_repo_item.filename }}'
        yum_repo_options: '{{ yum_repo_item.options }}'
      loop:
        - filename: CentOS-AppStream.repo
          options:
            AppStream:
              proxy: http://proxy.example.com:3128
        - filename: CentOS-Base.repo
          options:
            BaseOS:
              proxy: http://proxy.example.com:3128
        - filename: CentOS-Extras.repo
          options:
            extras:
              proxy: http://proxy.example.com:3128
      loop_control:
        loop_var: yum_repo_item


License
-------

[Apache-2.0](https://tldrlegal.com/license/apache-license-2.0-(apache-2.0))


Author Information
------------------

The [ganto.yum_repo](https://galaxy.ansible.com/ganto/yum_repo) role was written by Reto Gantenbein | [e-mail](mailto:reto.gantenbein@linuxmonk.ch) | [GitHub](https://github.com/ganto)
