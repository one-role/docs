# Inventory structure

As we are dealing with a number of different environments, like `dev`,
`tst`, `acc` and `prd` and more. And as we want to be somewhat flexible,
there is a stringent directory structure and setup for the inventory

Directory structure for the Ansible tree:

```
ansible
   │
   └── inventory               # Inventory directory
       ├─ group_vars           # Variables per group (preferred)
       │  ├─ all               # All default settings for all hosts and groups
       │  ├─ dev
       │  │  ├─ role1_vars     # Variable file for dev and role1
       │  │  └─ role2_vars     # Variable file for dev and role2
       │  ├─ tst
       │  ├─ acc
       │  └─ prd
       │
       ├─ host_vars            # Variables per host, override group_vars
       │  ├─ host1
       │  │  ├─ role1_vars     # Variable file for host1 and role1
       │  │  └─ role2_vars     # Variable file for host1 and role2
       │  ├─ host2
       │  └─ host3
       │
       ├─ dev
       │     ├─ group1         # Variable file for dev and group1
       │     ├─ group2         # Variable file for dev and group2
       │     ├─ group3         # Variable file for dev and group3
       │     └─ zz_groups      # Child group definition
       │
       ├─ tst
       │     ├─ group1         # Variable file for tst and group1
       │     ├─ group2         # Variable file for tst and group2
       │     ├─ group3         # Variable file for tst and group3
       │     └─ zz_groups      # Child group definition
       │
       ├─ acc
       │     ├─ group1         # Variable file for acc and group1
       │     ├─ group2         # Variable file for acc and group2
       │     ├─ group3         # Variable file for acc and group3
       │     └─ zz_groups      # Child group definition
       │
       ├─ prd
       │     ├─ group1         # Variable file for prd and group1
       │     ├─ group2         # Variable file for prd and group2
       │     ├─ group3         # Variable file for prd and group3
       │     └─ zz_groups      # Child group definition
       │
       └─ zz_groups      # Child group definition
```

The group files should contain the name of the _group_ and all hosts
residing in this group.

```
[dev_webservers]
web1.example.net
web2.example.net
```

Precede all the group names with the _environment_ where they are in, to
avoid name-clashes.  As all groups have the possibility to contain more
than one host, the group names should be _plural_, e.g.
`dev_webservers`.

In **every** environment directory there **should** be a file called
`zz_groups` or something like that. The requirement is that
file-globbing shows it as the last file in the directory. And this file
**must** contain **all** groups as children of the environment.

Something like this:

```
[dev:children]
dev_webservers
dev_mailservers
dev_dbservers
```
