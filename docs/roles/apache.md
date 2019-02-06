# Ansible apache role

A very simple role that ensures that the Apache webserver is installed
and running.

This also exposes the `restart_httpd` handler.

## Variables

## Variables

|   Variable name          | Default | Description                  |
|--------------------------|---------|------------------------------|
| `apache_modules_install` | `[]`    | Install these Apache modules |
| `apache_modules_enable`  | `[]`    | Enable these Apache modules  |
| `apache_modules_disable` | `[]`    | Disable these Apache modules |
| `apache_status` | `no` | Switch to configure Apache status page |
| `apache_status_url` | `/server-status` | Location of Apache status page |
| `apache_status_auth` | `yes` | Boolean to enable or disable basic authentication to access status page | 
| `apache_status_htpasswd_file` | `/etc/httpd/conf.d/020-server-status.htpass` | Path where Apache htpasswd will written to
| `apache_status_user` | `apache` | Username to access status page |
| `apache_status_pass` | `apache` | Password to access status page |

## Reason

As many functions use the Apache webserver, it seems sane to make
a separate role for that and add it as a requirement to all roles that
need Apache.

This role also opens incoming port 80 and 443.

## Requirements

Nothing special.


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
```

## License
MIT


## Author Information
  - Ton Kersten <tonk@smartowl.nl>


## Changelog
  - 2018-07-31 - Ton Kersten - Initial checkin
  - 2018-10-31 - Ton Kersten - Added modules
