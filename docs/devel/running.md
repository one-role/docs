# Running playbooks on the control node

The Ansible control node is the node that runs the Ansible playbooks.
It is made possible to use this system as a development environment.

!!! warning
    Be very careful to not to disturb the normal Ansible flow!!

In the directory `/etc/ansible/playbooks` (part of the `setup` module)
all Ansible playbooks reside, as all roles reside in the `roles`
directory. Normally there is a direct link between a playbook and a role
directory, e.g. the `dev.yml` playbook uses the `roles/dev` roles path.
This is all enforced through the `ansible_run` script in the `bin`
directory.

The normal set is:

| environment | command               | playbook      | roles directory   |
|:------------|:----------------------|:--------------|:------------------|
| Develop     | `ansible_run dev`     | `dev.yml`     | `roles/dev`       |
| Acceptance  | `ansible_run acc`     | `acc.yml`     | `roles/acc`       |
| Production  | `ansible_run prd`     | `prd.yml`     | `roles/prd`       |
| Service     | `ansible_run svc`     | `svc.yml`     | `roles/prd`       |
| Research    | `ansible_run rsc`     | `rsc.yml`     | `roles/dev`       |
| Masters     | `ansible_run masters` | `masters.yml` | `roles/prd`       |

When running the `ansible_run` command with just one parameter, like

```
bin/ansible_run prd
```

the `ansible-playbook` is run, using the `prd.yml` playbook and this
automatically selects the `roles/prd` role-set.

This is very good for _production_ runs, but inconvenient when
developing Ansible roles. To facilitate an environment for role
development, the `ansible_run` can take more than one parameter, where
the first is the playbook to run, without the `.yml` extension, and
second is a role-set to use. Example:

```
bin/ansible_run tonk kerstent
```

will run the playbook `playbooks/kerstent.yml` with the role-path
(environment) `roles/tonk`. The `tonk` roles-path is a local copy of the
e.g. `dev` set.  The `roles/tonk` will *not* be disturbed by the
`refresh` command and thus is available as a development tree.
This way it is possible for everybody to have his/her own development
tree on the master Ansible node.

After the role is complete and tested, it should be added to the
`roles.yml` file and submitted to `git`.


!!! warning
    **Never** run playbooks directly!!\
    Always use the `ansible_run` command!!

