# Deployment of code

When developing and changing modules a push to a Git branch (usually `dev`)
does not result in an update on the Ansible master directly.  When commits 
and pull-requests are merged with a branch a command to update the Ansible
configuration needs to be run by hand:

Log in to the Ansible control node and perform these commands:

```bash
cd /etc/ansible
./refresh -f
```

This causes a Git pull operation of the setup repository and of all the roles
listed in the `roles.yml` of the setup repository.
