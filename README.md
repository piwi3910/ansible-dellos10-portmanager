#Ansible Port manager for Dell OS10 switches

This ansible code will allow you to enable and disable ports for 1 or multiple switches to enable Cyber Recovery or other applications to break links between switches and block traffic flow.

## Pre-requirements

* ansible 2.9
* python 3.x
* paramiko 2.5.x

## Installation

pip3 install ansible==2.9.14

pip3 install paramiko==2.7.2

## Offline installation

Exectute this procedure if you have to install all your dependancies in an airgapped (no internet) host

you will need a host with internet access to execute this on.

1) install python3 via yum or apt and install pip3

example CentOS

```
yum install python3-pip
```

2) create a folder to download your needed pip packages
```
mkdir /tmp/mydeps
```

3) download the required packages
```
pip3 download -r requirements.txt -d /tmp/mydeps
```

4) now zip, tar or whatever way and transfer this archive to your offline host and unpack in the same location

5) install pip packagages from local offline repo
```
pip3 install --no-index --find-links /tmp/mydeps /tmp/mydeps/ansible-2.9.14.tar.gz
pip3 install --no-index --find-links /tmp/mydeps /tmp/mydeps/paramiko-2.7.2-py2.py3-none-any.whl
```

## how to use

1) set all switches with a name and ip in the inventory file
2) create a host var file per switch with the same name you used in the inventory file.
3) Create a vault file to keep the switch credentials safe

   ```ansible-vault create passwd.yml```

 **It will ask for a master passwords, Remember this we will need it later.**

 This will open up an editor
 provide user and passwords like:

 ```
 dellos10_sw1_user: admin
 dellos10_sw1_password: mypassword
 dellos10_sw2_user: admin
 dellos10_sw2_password: mypassword
 ```
 and so on, for as many switches your created earlier

 4) in the host var files created earlier, make sure you define all the ports you want to enable or disable. This is per switch so that you can have different ports per switch if needed

 ## Executing

 To execute this we need to provide the vault password, as now all the switch credentials are encrypted. Depending on what is possible there are 3 main ways of doing this.7.2

 There are 2 playbooks:
 * enable.yml -> this enables the ports
 * disable.yml -> this disables the ports

- - -
 ### manually

 ``` 
ansible-playbook -i inventory.yaml enable.yaml --extra-vars '@passwd.yml' --ask-vault-pass
 ```
 This will request the vault password before running the enable playbook. Change to disable if needed.
 
- - -

 ### automated via a vault password file.
 
 Create a file named **vault_pass.txt**
 In this file on the first line type in your vault password
 ```
chmod +600 vault_pass.txt
 ```

 now run 

 ```
ansible-playbook -i inventory.yaml enable.yaml --extra-vars '@passwd.yml' --vault-password-file vault_pass.txt
 ```

 - - - 

 ### Automated via a vault environment variable

 If you automation solution that can set an environment variable on run time, this would be the msot secure way
 There is a special script that will read this variable and pipe it into the vault pass file flag.

 set the following variable on runtime.

 ```MY_VAULT_PASSWORD```

 also make sure to

```
 chmod +x vault_password.sh
 ```

 now run it as

 ```
ansible-playbook -i inventory.yaml enable.yaml --extra-vars '@passwd.yml' --vault-password-file vault_password.sh
 ```