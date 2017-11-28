# Ansible Playbook: Openstack

An ansible playbook to deploy openstack components to a cluster.
# Overview
Initially the playbook was composed with a primary purpose to learn openstack deployment in a nutshell. 

As the project succesfully passes test environment the goal is changed to fill the gap between [install from source](https://github.com/openstack/openstack-ansible) and 
[docker deployment](https://github.com/openstack/kolla-ansible) , i.e. to create a deployment on bare metal hosts from official packages repository without containers and therfore eliminate addition level of related complexity.

At the current state, the playbook is able to deploy a fully functional openstack cluster(see below).
Also it's possible to deploy everything on a single(VM) host.

You are welcomed to read the playbook and feedback pull requests and suggestions :)

#### Basic high availability features implemented for controller/infrastructure services:
* MariaDB galera cluster
* RabbitMQ cluster
* Memcached service
* VIP cluster address managed by keepalived

#### So if more than one controller node configured, seemless failover is expected for:
* keystone
* glance
* cinder controller
* nova controller
* neutron controller
* heat
* horizon

# Description
#### The playbook is able to setup the core services described in the [official guide](https://docs.openstack.org/install-guide/openstack-services.html#):
* [**keystone**](https://docs.openstack.org/keystone/latest/)
* [**glance**](https://docs.openstack.org/glance/latest/)
* [**cinder**](https://docs.openstack.org/cinder/latest/)
* [**nova**](https://docs.openstack.org/cinder/latest/)
* [**neutron**](https://docs.openstack.org/neutron/latest/)
* [**heat**](https://docs.openstack.org/heat/latest/)
* [**swift**](https://docs.openstack.org/swift/latest/)
* [**ceilometer**](https://docs.openstack.org/ceilometer/latest/)
* [**horizon**](https://docs.openstack.org/horizon/latest/)

The configuration is _very_ simple:

It’s only required to place hostname(s) to the **controller** and **compute** groups in [hosts](hosts) file and carefully fill the required 
[group_vars](group_vars/all) parameters.

The playbook contains configuration files in roles directories. If you need to add or change any parameter you can edit
the configuration file which can be found in **roles/_service_/[files|templates]** directory.

Besides of cluster( or single host ) setup, the playbook also generates cluster manager configuration file located at **workdir/services.xml**.
Please visit [clinit manager home page](https://github.com/sergevs/clinit) and see 
[manual](https://github.com/sergevs/clinit/wiki). The rpm package can be downloaded from [clinit-1.0-ssv1.el6.noarch.rpm](https://github.com/sergevs/clinit/releases/download/1.0/clinit-1.0-ssv1.el6.noarch.rpm).
After clinit package installed you’ll be able to stop, start and see status of services on the cluster.

# Configuration
Services configuration performed using the hosts and variables files. 
#### Hosts file:
The empty  file is supplied with the playbook. Please examine [hosts](hosts) and supply appropriate host names. 
**You must not remove any existing group**. Leave the group empty if you don't need services the group configures. The same hostname can be placed to any hosts group.
As an instance if you want setup everything on one host, just put the same hostname to each hosts group.
As far, only **controller** and **compute** groups are well tested and supported.

#### Variables file:
Please examine [group_vars/all](group_vars/all) and supply appropriate configuration for your network environment and disk storage parameters.

# Usage
## Prepare,verifty repositories configuration and perform a basic check:

    ansible-playbook -i hosts -t prepare site.yml
    ansible-playbook -i hosts -t check site.yml

## Deployment:

    ansible-playbook -i hosts site.yml

if you have installed clinit, after deployment you can also run:

    clinit -S workdir/services.xml status
    clinit -S workdir/services.xml tree

#### Tags used in playbook:
* **package** : install rpm packages
* **config** : deploy configuration files, useful if you want just change configuration on hosts.
* **test** : run test actions

Also most hostgroups have the tag with similar name.

# Requirements
* [Ansible >= 2.2.0.0 ](http://www.ansible.com) is required.
* Openstack version: **liberty**, **mitaka**, **ocata**, **pike** - please use appropriate branch, **pike** is currently at the master branch.
* **remote_user = root** must be configured for ansible.

# Target host(s) requirements
* At least 8 Gb RAM, 4 CPU cores, 10Gb HDD (5Gb for root FS + 5GB cinder partition) is required for minimal single host test installation.
* OS version: Redhat/CentOS 7.4 with current updates.(**updates are important**).
* The required for Openstack repositories have to be properly configured.
* SSH key passwordless authentication must be configured for root account.
* **se_linux** must be disabled.
* **requiretty** should be switched off in **/etc/sudoers** file.
* 2 interfaces must be present: one for private and one for **provider(public)** network.
* at least one spare partition must be available for **cinder**( block storage ) service.

## License

[MIT](LICENSE)
