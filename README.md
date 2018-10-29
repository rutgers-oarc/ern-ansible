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

- roles = like modules. In this case 2 roles: `hosts` (generates /etc/hosts file) and `slurmctld` (
- tasks = individual pieces of instruction to perform on remote hosts
- handlers = services need to be notified when they need to be restarted
