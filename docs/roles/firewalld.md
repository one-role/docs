# Ansible firewalld role

Role for the default configuration of Firewalld

## Requirements

Make sure you run this role _after_ installation of various software packages. Somtimes Firewalld service files are added during package installation. Attempting to add a service for which nog definition file has been installed yet may end up in a failed state.

## Configuration

Firewalld can be configured with a list called `firewalld_config` containing the following key-value pairs. Either `service` OR (read: xor) `port` are required. The names of these fields match the resp. options of the [`firewalld`](https://docs.ansible.com/ansible/2.6/modules/firewalld_module.html) module.

| `firewalld_config:` | Default                  | Description
|---------------------|--------------------------|------------
| `immediate`         | `True`                   | Should this configuration be applied immediately.
| `masquerade`        | -                        | Enable/Disable masquerade setting from/to a zone (so this requires a zone!).
| `permanent`         | `True`                   | Should this configuration persist across reboots.
| `port`              | -                        | Name of a port or port range to add/remove to/from firewalld.
| `rich_rule`         | -                        | Rich rule to add/remove to/from firewalld.
| `service`           | -                        | Name of a service to add/remove to/from firewalld.
| `state`             | `enabled`                | Enable or disable a setting.
| `zone`              | `system-default(public)` | Firewalld zone to operate on.


## Example Playbook

```
---
# Example playbook

# Set all dynamic groups
- import_playbook: playbooks/pre.yml

# All common things
- hosts: ansiblemanaged_True
  roles:
    - firewalld
  vars:
    firewalld_config:
      - port: 123/udp
        state: disabled
      - port: 7389/tcp
        state: enabled
      - service: mysql
        state: enabled
      - service: ssh
```

## License
MIT


## Author Information
  - Ton Kersten <tonk@smartowl.nl>
  - Stefan Joosten <stefan@atcomputing.nl>
  - Richard Schutte <richard@atcomputing.nl>


## Changelog
  - 2018-09-12 - Stefan Joosten - Add `firewalld_config`
  - 2018-07-30 - Ton Kersten - Initial checkin
