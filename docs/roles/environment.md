# Ansible environment role

Set all proxy environments.\
The proxy settings are defined in the `group_vars`

## TODO
* **FIXME**: This role does not adhere to [Coding Style](http://ansible.svc.example.net/docs/devel/codingstyle/) !
* update this documentation


## Requirements

Nothing special.

## Example Playbook

```
---
# Example playbook

# Set all dynamic groups
- import_playbook: playbooks/pre.yml

# Set the proxy settings
- hosts: ansiblemanaged_True
  roles:
    - environment
```

## License
MIT


## Author Information
  - Ton Kersten <tonk@smartowl.nl>


## Changelog
  - 2018-08-07 - Ton Kersten - Initial checkin
