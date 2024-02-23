# Lab 1.1: Installing Ansible
In this exercise, you will install Ansible on a workstation node running Ubuntu Linux.


##### Outcome
You should be able to install Ansible on a workstation node.

1. Configure the PPA on your system and install Ansible

Update repository
```lua
sudo apt update
```
Install common properties
```lua
sudo apt install -y software-properties-common
```
Add repository
```lua
sudo add-apt-repository --yes --update ppa:ansible/ansible
```
Install ansible
```lua
sudo apt install -y ansible
```
2. Verify that Ansible is installed on the system. Execute the ansible command with the -- version option.
```lua
ansible --version
```
3. Verify the ansible_python_version on the localhost by using the setup module.
```lua
ansible -m setup localhost | grep ansible_python_version
```
