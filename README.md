# k8s_cluster_with_ansible_

This repository has set of ansible playbooks created to setup a kubernetes cluster fully automated with one master and multiple worker nodes. This will work on physical servers, virtual machines, aws cloud, google cloud or any other cloud servers. This has been tested and verified on Centos 7.3 64 bit operating systems. 

How to use this (Setup Instructions):

Make your servers ready (one master node and multiple worker nodes).

Make an entry of your each hosts in /etc/hosts file for name resolution.

Make sure kubernetes master node and other worker nodes are reachable between each other.

Internet connection must be enabled in all nodes, required packages will be downloaded from kubernetes official yum repository.

Clone this repository into your master node.

git clone https://github.com/agkanon143/k8s_cluster_with_ansible.git

once it is cloned, get into the directory

cd kubernetes-and-ansible/centos

There is a file "hosts" available in "centos" directory, Just make your entries of your all kubernetes nodes.

Deploy the ssh key from master node to other nodes for password less authentication.

ssh-keygen

Copy the public key to all nodes including your master node and make sure you are able to login into any nodes without password.

ssh-copy-id user@node

Update playbook/configure_master_node.yml file with master node ip address

Run "settingup_kubernetes_cluster.yml" playbook to setup all nodes and kubernetes master configuration.

ansible-playbook settingup_kubernetes_cluster.yml

Run "join_kubernetes_workers_nodes.yml" playbook to join the worker nodes with kubernetes master node once "settingup_kubernetes_cluster.yml" playbook tasks are completed.

ansible-playbook join_kubernetes_workers_nodes.yml

Verify the configuration from master node.

kubectl get nodes

What are the files this repository has?:

ansible.cfg - Ansible configuration file created locally.

hosts - Ansible Inventory File

settingup_kubernetes_cluster.yml - Ansible Playbook to perform prerequisites ready, setting up nodes, configure master node.

configure_worker_nodes.yml - Ansible Playbook to join worker nodes with master node.

clear_k8s_setup.yml - Ansible Playbook helps to delete entire configurations from all nodes.

playbooks - Its a directory holds all playbooks.
