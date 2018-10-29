# Ansible for ERN 

At this point, this is a somewhat general way to automate some tasks for ERN (Eastern Regional Network). 

- handle `/etc/hosts` file in somewhat general way, namely: 
    - each hostname and host ip is specified in the group_vars directory, so that it can be used and reused for multiple tasks (for now only generating the /etc/hosts file, but in the future, also opening firewall only for the ips specified in the network).
- modify `/etc/slurm/slurm.conf` file in order to insert 2 lines that specify jobcomp type and location - namely elastic search. 

## Tree structure

```
├── group_vars
│   ├── all
│   └── networks
├── hosts
└── roles
    ├── hosts
    │   ├── tasks
    │   │   └── main.yml
    │   └── templates
    └── slurmctld
        ├── handlers
        │   └── main.yml
        └── tasks
            └── main.yml
```

## Small primer on ansible 

- inventory = list of machines on which to execute the tasks. This is in the `hosts` file on top level. For now contains only mace. 
- roles = like modules. In this case 2 roles: 
    - `hosts` (generates /etc/hosts entries) and 
    - `slurmctld` (change in slurm.conf i.e. reconfiguring slurmctld)
- tasks = individual pieces of instruction to perform on remote hosts
- handlers = services need to be notified when they need to be restarted. You don't want to restart the service after every taks - only when needed. 
- templating = jinja2 templates have several tips and tricks. In particular, strings can be transformed easily. 
    - `{{ item.name }}` will evaluate to the value of `item.name`
    - `{{ item.alias | default([]) | join(' ') | regex_replace('^(\\w)',' \\1') }}`  will start with item.alias list and send it through pipe (`|`) to replace with empty list if not defined, then join with spaces then add a space at the beginning of the string if the string starts with a word character. 


## Some reasons for choosing this design

- the reason for not pushing the whole `/etc/hosts` file as a whole is that it would rewrite whatever the local people already have there in case of an update (e.g. because of adding a new member to the federation). In general, it is better to write ansible tasks so that particlular lines are modified. The intent of the task is clearer, and the scope of change is smaller. 
- the reason for not making ip a hostvar but rather a group var is that every host needs to be able to talk to every other host. So every host needs to be aware of all the other ip's in the federation. 
