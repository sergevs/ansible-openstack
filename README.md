# Ansible Playbook: Openstack

An ansible playbook to deploy openstack components to the cluster
# Overview
The playbook is composed according to [official openstack guides](http://docs.openstack.org/mitaka/install-guide-rdo/) 
with a primary purpose to learn openstack deployment in a nutshell. Another reason is to fill the gap between official 
[full-fledged](https://github.com/openstack/openstack-ansible) and 
[devel](http://docs.openstack.org/developer/openstack-ansible/developer-docs/quickstart-aio.html) deployment guides. 
At the current state the playbook is able to deploy a fully functional openstack cluster.
Also it's possible to deploy everything on a single(VM) host.
Work on HA deployment is in progress.
So if you are looking for a simple setup and can afford the lack of HA - than you are on the right place. 
Also please read requirements section carefully.

# Description
#### The playbook is able to setup the core services described in the [official guide](http://docs.openstack.org/mitaka/install-guide-rdo/):
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

It’s only required to place hostname(s) to the **controller** and **compute** groups in [hosts](hosts) file and carefully fill the required 
[group_vars](group_vars/all) parameters.

The playbook contains configuration files in roles directories. If you need to add or change any parameter you can edit
the configuration file which can be found in roles/_service_/[files|templates] directory.

Besides of cluster( or single host ) setup, the playbook also generates cluster manager configuration file located at **workdir/services.xml**.
Please visit [clinit manager home page](https://github.com/sergevs/clinit) and see 
[manual](https://github.com/sergevs/clinit/wiki). The rpm package can be downloaded from [clinit-1.0-ssv1.el6.noarch.rpm](https://github.com/sergevs/clinit/releases/download/1.0/clinit-1.0-ssv1.el6.noarch.rpm).
After clinit package installed you’ll be able to stop, start and see status of services on the cluster.

# Configuration
Service configuration performed using the hosts file. The empty [hosts](hosts) file is supplied with the playbook.
**You must not remove any existing group**. Leave the group empty if you don't need services the group configures. The same hostname can be placed to any hosts group.
As an instance if you want setup everything on one host, just put the same hostname to each hosts group.
As far, only **controller** and **compute** groups are well tested and supported.

#### Variables parameters:
Please see [group_vars/all](group_vars/all) and supply appropriate configuration for the required networking and disk partition parameters.

# Usage
To start deployment run:

    ansible-playbook -i hosts site.yaml

if you have installed clinit, after deployment you can also run:

    clinit -S workdir/services.xml status
    clinit -S workdir/services.xml tree

#### Tags used in playbook:
* **package** : install rpm packages
* **config** : deploy configuration files, useful if you want just change configuration on hosts.
* **test** : run test actions

Also most hostgroups have the tag with similar name.


# Requirements
[Ansible >= 2.1.1.0 ](http://www.ansible.com) is required. Please read [official documentation](http://docs.ansible.com/ansible/intro_installation.html#latest-release-via-yum) to install it. 

Openstack version: liberty or mitaka - please use appropriate branch, mitaka is currently at the master branch.

**remote_user = root** must be configured for ansible.

# Target host(s) requirements
The required for Openstack repositories have to be properly configured.

OS version: Redhat/CentOS 7

SSH key passwordless authentication must be configured for root account.

**se_linux** must be disabled.

**requiretty** should be switched off in **/etc/sudoers** file.

2 interfaces must be present and one available for **provider(public)** network.

at least one free partition must be available for **cinder**( block storage ) service.

## License

[MIT](LICENSE)
