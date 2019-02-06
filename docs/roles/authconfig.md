# Ansible authconfig role
Role to make sure that authconfig works with the Univention LDAP server
and configure `sudo` to work with ldap.


## Requirements
- koichirok.authconfig-module


## Example Playbook

```
---
# Example playbook
- authconfig:
    enableldap: yes
    enableldapauth: yes
    enableldaptls: no
    ldapserver=ldap://127.0.0.1/
    ldapbasedn=dc=example,dc=com
```

## License
MIT


## Author Information
------------------
  - Ton Kersten <tonk@smartowl.nl>


## Changelog
  - 2018-07-24 - Ton Kersten - Initial checkin
