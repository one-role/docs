# Directory structure

The final directory structure on the Ansible Control node.

```
ansible
   │
   ├── inventory               # Inventory directory
   │   ├─ dev                  # All hosts in the dev group
   │   ├─ tst                  # All hosts in the tst group
   │   ├─ acc                  # All hosts in the acc group
   │   ├─ prd                  # All hosts in the prd group
   │   │
   │   ├─ group_vars           # Variables per group (preferred)
   │   │  ├─ dev               # Group variables for group dev
   │   │  │  ├─ role1_vars     # Variable file for dev and role1
   │   │  │  └─ role2_vars     # Variable file for dev and role2
   │   │  ├─ tst
   │   │  ├─ acc
   │   │  └─ prd
   │   │
   │   ├─ host_vars            # Variables per host, override group_vars
   │   │  ├─ host1
   │   │  │  ├─ role1_vars     # Variable file for host1 and role1
   │   │  │  └─ role2_vars     # Variable file for host1 and role2
   │   │  ├─ host2
   │   │  └─ host3
   │   └─ zz_groups
   │
   ├── playbooks               # Simple playbooks. As little as possible
   │   ├─ update.yml
   │   ├─ dev.yml
   │   ├─ tst.yml
   │   ├─ acc.yml
   │   ├─ prd.yml
   │   └─ tasks
   │        ├─ common.yml
   │        ├─ masters.yml
   │        ├─ play1.yml
   │        └─ play2.yml
   │
   ├── galaxy                  # All roles downloaded from the Galaxy
   │
   └── roles                   # All locally developed roles
       ├─ dev                  # Local environments (dev, acc, prd, etc)
       │  ├─ role1
       │  │  ├─ defaults
       │  │  ├─ files
       │  │  ├─ handlers
       │  │  ├─ meta
       │  │  ├─ tasks
       │  │  ├─ templates
       │  │  └─ vars
       │  ├─ role2
       │  └─ role3
       ├─ tst
       ├─ acc
       └─ prd
```

This complete structure is managed through `git` and the `setup/refresh`
command.
