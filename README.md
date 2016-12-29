##Ansible journey of an Symfony developer

###Getting started

Ansible is a configuration management with an agent-less approach, meaning there is no pre-installed software needed in remote machines.

`SSH` is first-class citizen in terms of conectivity.

`ansible` is needed only in control machine. To install Ansible from Mac with Brew:

```bash
brew install ansible
```

To verify ansible is successfully installed in control machine

```bash
$ ansible --version
ansible 2.2.0.0
  config file =
  configured module search path = Default w/o overrides
```

###Configuration file
Loaded in the following order

```
* ANSIBLE_CONFIG (an environment variable)
* ansible.cfg (in the current directory)
* .ansible.cfg (in the home directory)
* /etc/ansible/ansible.cfg
```

Sample config https://raw.githubusercontent.com/ansible/ansible/devel/examples/ansible.cfg 


###Inventory

Hosts or remote machines are defined in inventory files and can be grouped, nested.

```ini
[webservers]
foo.example.com
bar.example.com
www[01:50].example.com

[dbservers]
one.example.com
two.example.com
db-[a:f].example.com
```

Assign variable per host or per group. However, the preferred practice in Ansible is actually not to store variables in the main inventory file `group_vars/groupfoo` and `host_vars/hostbar`


```ini
[dev]
devlocal ansible_port=22 ansible_host=192.0.2.50

[atlanta]
host1 http_port=80 maxRequestsPerChild=808

[dev:vars]
self_destruct_countdown=60
```


Creating group of group
```ini
[singapore]
sg1

[hongkong]
hk1

[southeast:children]
singapore
hongkong
```

###Dynamic Inventory
See list of supported vendors https://github.com/ansible/ansible/tree/devel/contrib/inventory

###Host matching pattern

`ansible <host_matching_pattern> -m <module_name> -a <arguments>`

- `*` and `all` match all hosts
- `*.example.com` wildcard matching
- `webservers:dbservers` matches hosts in multiple group (OR)
- `webservers:&dev` maches hosts belongs to both groups (AND)
- `webservers:!{{excluded}}:&{{required}}` using variable
- `~(web|db).*\.example\.com` regex matching

###Ad-hoc command

Execute abitrary command
```bash
ansible hosts -a '/bin/echo foo'
```

Execute module command

```bash
ansible webservers -m yum -a "name=acme state=present"
```
Execute long runing command in the background

```bash
ansible all -B 3600 -P 0 -a "/usr/bin/long_running_operation --do-stuff"
```