# ansible_lab

- Supported OS: Debian, Ubuntu
- Ansible version: 2.5.1

## APT repository configuration
Universe repository must be enabled to install openjdk-8 package.
For Cassandra role, Datastax APT repository is automatically configured, provided you enter your customer user name and key in Cassandra role defaults:

```
 $ vi <your dir>/ansible_lab/roles/cassandra/defaults/main.yml
  ...
  repo:
    ...
    user_key: "<your customer key>"
    user_name: "<your customer login>"
 ```
 
## Dev environment
- Install git and ansible packages:
```
  $ sudo apt-get update
  $ sudo apt-get install git software-properties-common
  $ sudo apt-add-repository --yes --update ppa:ansible/ansible
  $ sudo apt-get install ansible
```
- Deploy root SSH public key from ansible server to target hosts
- Go to your work directory and clone this project:
```
  $ cd <your dir>
  $ git clone https://github.com/aetile/ansible_lab.git
```
- Update roles_path parameter in Ansible configuration file:
```
  $ cd <your dir>/ansible_lab
  $ vi ansible.cfg
```

## Using Apache role
This role installs two website templates (contained in industrie.zip and sportz.zip archives) on the same host using name resolution on port 80 instead of proxy port redirection.
To test this role:

- Update apache inventory in <your dir>/ansible_lab/inventories/apache/hosts.ini
- Update IPs in role defaults <your dir>/ansible_lab/roles/apache/defaults/main.yml or override defaults in group_vars
- Add the following lines to name resolution on your laptop (where you will run a browser to check websites):
```
  $ vi /etc/hosts (C:\Windows\System32\Drivers\etc\hosts on Windows laptop)
  <ansible target IP> example2 example2.com www.example2.com
  <ansible target IP> example3 example3.com www.example3.com
```
- execute apache.yml playbook on apache inventory
```
  $ cd <your dir>/ansible_lab
  $ ansible-playbook -i <your dir>/ansible_lab/inventories/apache/hosts.ini apache.yml
```
- Open a browser and test websites url http://www.example2.com and http://www.example3.com

## Using Cassandra role
This role installs Datastax Enterprise and Opscenter on the cassandra inventory.
To test this role:

- Update cassandra inventory in [your dir]/ansible_lab/inventories/cassandra/hosts.ini
- Update IPs in role defaults [your dir]/ansible_lab/roles/cassandra/defaults/main.yml or override defaults in group_vars
- execute cassandra.yml playbook on cassandra inventory

To install or reinstall DSE and Opscenter run the following:

```
  $ cd <your dir>/ansible_lab
  $ ansible-playbook -i <your dir>/ansible_lab/inventories/cassandra/hosts.ini cassandra.yml -e "cleaninstall=false"
```

To uninstall and make a clean install run the following:

```
  $ cd <your dir>/ansible_lab
  $ ansible-playbook -i <your dir>/ansible_lab/inventories/cassandra/hosts.ini cassandra.yml
```
- Check cluster status:
```
  $ su - cassandra
  $ nodetool status -r
```
- Check Opscenter web UI at http://[opscenter host] or http://[opscenter host]:8888

## Cassandra role with opensource Cassandra
If you need to install the Cassandra opensource version, do the following:

- Edit the package list to be installed in role defaults and replace dse packages with cassandra package or override defaults in group_vars:
```
  $ vi <your dir>/ansible_lab/roles/cassandra/defaults/main.yml
```
- Remove hosts from opscenter group in cassandra inventory (the opscenter group must still be there but empty):
```
  $ vi <your dir>/ansible_lab/inventories/cassandra/hosts.ini
```
- Replace dse service start with cassandra service start in role tasks:
```
  $ vi <your dir>/ansible_lab/roles/cassandra/tasks/main.yml
```
