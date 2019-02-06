# Ansible wiki role

This roles installs the certificates needed for the Confluence server

## Requirements

This role needs Apache and vhosts


## Example Playbook

```
---
# Example playbook

# Set all dynamic groups
- import_playbook: playbooks/pre.yml

# All common things
- hosts: ansiblemanaged_True
  roles:
    - apache
    - vhosts
    - wiki
```

## Copy the thing

To make things tick all software and data is copied from one host to
the other, at least these directories and files:

- `/usr/local/confluence`
- `/usr/local/confluence-data`
- `/etc/init.d/confluence`

### The server.xml

In the `server.xml` the hostname is defined and this should match the
name of the current host.

Make sure the `server.xml` contains:

```
proxyName="<fqdn of the machine>"
```

## License
MIT


## Author Information
  - Ton Kersten <tonk@smartowl.nl>


## Changelog
  - 2018-11-26 - Ton Kersten - Initial checkin
