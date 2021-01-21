# Example setup for Zabbix environment via Ansible

Table of Content:
- [Example setup for Zabbix environment via Ansible](#example-setup-for-zabbix-environment-via-ansible)
- [Introduction](#introduction)
- [Files](#files)
  * [Vagrantfile](#vagrantfile)
  * [bastion.yml - node.yml](#bastionyml---nodeyml)
  * [ansible/hosts](#ansible-hosts)
  * [ansible/group_vars/all](#ansible-group-vars-all)
  * [ansible/group_vars/database](#ansible-group-vars-database)
  * [ansible/group_vars/zabbix-server](#ansible-group-vars-zabbix-server)
  * [ansible/requirements.yml](#ansible-requirementsyml)
  * [ansible/zabbix-server.yml](#ansible-zabbix-serveryml)
  * [ansible/zabbix-proxy.yml](#ansible-zabbix-proxyyml)
  * [ansible/zabbix-agent.yml](#ansible-zabbix-agentyml)

# Introduction

This repository contains a basic setup working in a Vagrant setup that is able to run the following Zabbix components:

* Zabbix Server;
* Zabbix Frontend;
* Zabbix Proxy;
* Zabbix Agent;

Ansible will be used and this repository contains the code for a 3 blogposts for Zabbix.com.

# Files

The following provides an overview of certain files and their purpose.

## Vagrantfile
This contain the configuration of the nodes we are using to setup our environment. We have 1 node called `bastion` on which Ansible is executed and the nodes that will be running the components which we will be installing.

## bastion.yml - node.yml
These are the basic Ansible playbooks that are used when we create the Virtual Machines, we install some basic Ubuntu packages, Python modules and the ssh key before we can do anything.

## ansible/hosts

This is an ini file (if you are already a Ansible user, yours could also be a yaml version) that contains the inventory of all hosts that are installed/configured by Ansible. This is a file that you normally create yourself and is not generated, so you basically decide for yourself which hosts are maintained by Ansible.

We will install Ansible in an extra host called `bastion`, so we don't mess a lot with the current host. There are 4 groups, called `zabbix_server`, `zabbix_database`, `zabbix_proxy` and `database` (We ignore the `local` group.).

The `zabbix_server` group has 1 node, called `node-1` and has an ip of `10.10.1.11` which is set with the variable name `ansible_host`. The `zabbix_database` group also has 1 node, called `node-2` and has an ip of `10.10.1.12`. The `zabbix_proxy` has also 1 node called `node-3` and has an ip of `10.10.1.13`.

The `database` group doesn't contain any nodes directly like the other groups that we just described. But the `database` group contains the 2 groups of `zabbix_proxy` and `zabbix_database`. So if we add another node to one of these 2 groups, then the node will automatically belong to the `database` group.

## ansible/group_vars/all

This file contains all settings that are used for all servers, so you can see these as the defaults we are going to use for the Zabbix Server and the database server. We have configured the basics for Zabbix, like on which url Zabbix is available (`zabbix.example.com`), the Admin username/password so Ansible is able to create hosts, action, media and the users. It also provides which Templates we default want to be used for when we create the hosts.

## ansible/group_vars/database

This file contains the settings that applies to all hosts in the "database" group. As we are going to deploy MySQL database, we have to provide some information on which we will dive in and discuss in a later blog post.

## ansible/group_vars/zabbix_server

The settings/configuration the Zabbix Server needs when we want to configure it correctly. We provide the database information, where is MySQL running, what credentials do we have to use and what password we will create for the zabbix-server user. It also provides that we are using Apache as a front end and we also configure the Zabbix Server template. The file `ansible/group_vars/zabbix_proxy` is basically the same, but for the Zabbix Proxy.

## ansible/requirements.yml

The nice thing about Ansible is, that you should not write everything yourself. There is a lot of code freely available which we are using to configure our environment. In Ansible we make use of roles and an collection. An Ansible role is basically a set of files/templates/tasks in a specific directory layout with the purpose to install/configure a specific component, like MySQL or Apache. There is also a collection, this is basically a set of specific Ansible Modules and Ansible Roles into a - what Ansible calls - an Collection. In our case, we make use of the Ansible collection for Zabbix which contains the Zabbix Roles and some module which we will be using to configure our infrastructure. In this `requirements.yml` file we configure the dependencies which we need to deploy the infrastructure.

To make sure it works, we used the latest working versions in this requirements file. This would give us

## ansible/zabbix-server.yml

This is the playbook which will be used for installing and configuring the Zabbix Server node(s).

## ansible/zabbix-proxy.yml

This is the playbook which will be used for installing and configuring the Zabbix Proxy node.

## ansible/zabbix-agent.yml

This is the playbook which will be used for installing and configuring the Zabbix Agent on all of the nodes.
