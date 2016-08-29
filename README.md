# Ansible Playbook: Openstack

An ansible playbook to deploy openstack components to the cluster
# Overview
The playbook is composed according to [official openstack guides](http://docs.openstack.org/liberty/install-guide-rdo/) 
with a primary purpose to learn openstack deployment. Another reason is to fill the gap  between official 
[full-fledged](https://github.com/openstack/openstack-ansible) and 
[devel](http://docs.openstack.org/developer/openstack-ansible/developer-docs/quickstart-aio.html) guides. 
So if you are looking for a simple a cluster setup for learning or development purposes - than you are on the right place. 
Also please read requirements section carefully.

# Description
#### The playbook is able to setup the core services described in the guide:
* **keystone**
* **glance**
* **cinder**
* **nova**
* **neutron**
* **heat**
* **swift**
* **ceilometer**
* **horizon**

The configuration is _very_ simple:

It’s only required to place hostname(s) to the **controller** and **compute** groups in [hosts](hosts) file, and the required services will be setup.

The playbook contains configuration files in roles directories. If you need to add or change any parameter you can edit
the configuration file which can be found in roles/_service_/[files|templates] directory.

Besides of cluster( or single host ) setup, the playbook also generates cluster manager configuration file located at **workdir/services.xml**.
Please visit [clinit manager home page](https://github.com/sergevs/clinit) and see 
[manual](https://github.com/sergevs/clinit/wiki) .The rpm package can be downloaded from [clinit-1.0-ssv1.el6.noarch.rpm](https://github.com/sergevs/clinit/releases/download/1.0/clinit-1.0-ssv1.el6.noarch.rpm).
After clinit package installed you’ll be able to stop, start and see status of services on any node.

# Configuration
Service configuration performed using the hosts file. The empty [hosts](https://github.com/sergevs/ansible-openstack/blob/master/hosts) file is supplied with playbook.
**You must not remove any existing group**. Leave the group empty if you don't need services the group configures. The same hostname can be placed to any hosts group.
As aninstance if you want setup everything on one host, just put the same hostname to each hosts group.
As far, only **controller** and **compute** groups are tested and supported.

#### Variables parameters:
Please see [group_vars](https://github.com/sergevs/ansible-openstack/tree/master/group_vars) and supply appropriate configuration for the required
networking and disk partition parameters.

# Usage
To start deployment run:

    ansible-playbook -i hosts site.yaml

if you have installed clinit you can also run:

    clinit -S workdir/services.xml status
    clinit -S workdir/services.xml tree

#### Tags used in playbook:
* **package** : install rpm packages
* **config** : deploy configuration files, useful if you want just change configuration on hosts.
* **test** : run test actions

Also most hostgroups have the tag with similar name.


# Requirements
[Ansible >= 2.1.1.0 ](http://www.ansible.com) is required. Please read [official documentation](http://docs.ansible.com/ansible/intro_installation.html#latest-release-via-yum) to install it. 

OS version: Redhat/CentOS 7

Openstack version: liberty or mitaka - please use appropriate branch, mitaka currently in the master branch.

The required for Openstack repositories have to be properly configured on the target hosts.

SSH key passwordless authentication must be configured for root account for all target hosts.

**se_linux** must be disabled

**requiretty** should be switched off in /etc/sudoers file on the target hosts.

**remote_user = root** must be configured for ansible.

## License

[MIT](https://github.com/sergevs/ansible-openstack/blob/master/LICENSE)
