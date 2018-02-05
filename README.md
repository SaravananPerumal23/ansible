# ansible
Repo for the Ansible Playbooks

## Pre-Requisite
  1. Install Vagrant
  2. Install VirtualBox

## Getting Started
This Repo has a Vagrant File which will be helpful to spin up Virtual Instances to play with Ansible Playbooks

The vagrant file has the box name which can be choosen from this [link](https://app.vagrantup.com/boxes/search)

### Starting the Virtual Instances

```
vagrant up
```

For updating the existing Vagrant boxes

```
vagrant box update
```

With this step, you should see 2 virtual instances (centos-7 instance & Windows2012R2 instance) created in your VirtualBox

You should be able to access both these instances using default Vagrant credentials. Linux instances through SSH & windows instances with the Vagrant/Administrator logon from VirtualBox

To get started, you should be able to install Ansible in the Linux/Centos instance.

### Setting up Ansible Control Center

Login to the Linux/Centos instance with the below command and provide the default password when prompted
```
ssh vagrant@192.168.10.20
```

Note: If on VPN connection, the direct connection to the connection can be done using this below Command
```
ssh -p 2222 vagrant@localhost
```

Installing the Pre-requisites and Ansible. NOTE: sudo is root user and yum is package manager for Centos

```
sudo yum install epel-release
sudo yum install python-pip
sudo pip install --upgrade pip
sudo pip install markupsafe
sudo pip install xmltodict
sudo pip install pywinrm
sudo pip install ansible
sudo yum install sshpass
```

Verify your Ansible installation

```
ansible --version
```

Uploading the Ansible playbook and files to Control Server

```
vagrant up --provision
```

### Few configuration that need to be done in remote instances

#### Windows
For Windows instance, you need to enable WinRM logging

From a powershell command window run this command

```
Set-Item -Path "WSMan:\localhost\Service\AllowUnencrypted" -value True
```

From a Command window run this command and include Vagrant user to this domain group and enable Execute permission

```
winrm configSDDL default
```
For details check with this [link](https://stackoverflow.com/questions/38105486/winrm-the-specified-credentials-were-rejected-by-the-server)

Issues Related to Windows winping

Issue: ntlm: the specified credentials were rejected by the server
Resolution: Try changing the username in inventories/dev/group_vars/all.yml file to administrator to see if that works. If so, then the Winrm is working and not for non-admin users.
Check in Event Viewer logs and if the Login failed due to the password expiry, you need to login as that user and reset the password.

Let's start using Ansible to check if it is able to communicate with the Windows instance that we have spinned up. Inventories can set in ansible.cfg file or it can be passed during runtime

```
ansible windows -i inventories/localbox/hosts -m win_ping
```
In the above command, windows is the host name configured in localbox/hosts file and win_ping is a specific module of ansible to ping other instances

If incase you have multiple hosts group that you want to perform Ping, you can use the below command

```
ansible windows,linux -i inventories/dev/hosts -m win_ping -e "ansible_user=USER ansible_password=PWD"
```

Running playbooks

```
ansible-playbook winlogbeat.yml
ansible-playbook filebeat.yml
```
To get more logs on the actions performed while executing playbook, you could use the below command with Verbose mode

```
ansible-playbook winlogbeat.yml -vvv
```

To override the variable from the command line while executing use the below Command
Providing just user credentials
```
ansible-playbook  windowsServices.yml --ask-vault-pass -e "ansible_user=USER ansible_password=USER"
```
Providing hosts as input parameter when hosts is accepted as a variable
```
ansible-playbook  windowsServices.yml --ask-vault-pass -e "hosts=windows ansible_user=USER ansible_password=USER"
```
Providing the inventory also in command
```
ansible-playbook -i inventories/payments_ip/hosts windowsServices.yml --ask-vault-pass -e "hosts=appserver-test ansible_user=USER ansible_password=PWD service_account=USER service_account_password=PWD"
```

For Centos Intance, need to use the root credentials

```
ansible-playbook filebeat.yml --ask-become-pass
```

## Ansible Vault
Vault is a feature within Ansible to store sensitive information such as passwords, certificates and other keys which are not supposed to be in plain text.

Encrypting .YML file which has sensitive information
```
cd ansible
ansible-vault encrypt inventories/localbox/group_vars/all/windows.yml
```
This command enables to encrypt the file with a password which will be prompted on command execution. This password will be required whenever your playbooks requires the variables stored in this .YML file.

For using this encrypted file in your playbook, you would need to use this command to provide with the password to make use of this file variables

```
ansible windows -i localbox/hosts -m win_ping --ask-vault-pass
```

This encrypted file will be added to your source repository (currently Github) and whenever need arises to update this file, you could use the below command to edit and update the file which automatically keeps this file encrypted post edit.

```
ansible-vault edit inventories/localbox/group_vars/all/windows.yml
```

If you wanted to completely decrypt the file and store it in plain text(assuming sensitive information is removed)

```
ansible-vault decrypt inventories/localbox/group_vars/all/windows.yml
```

## Few Issues and resolution

Issue: Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host.
Resolution: Adding this command to Ansible.cfg file so that host key is disabled 'host_key_checking = false'


## ELK Setup

```
ansible-playbook elk.yml --ask-become-pass
```
