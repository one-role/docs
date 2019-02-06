# Running playbooks

The inventory structure allows for very fine-grained selection of hosts
or host-groups. An example playbook could be:

```.yaml+jinja linenums="1" hl_lines="7 15 23"
# Development wide playbook

# Set all dynamic groups
- import_playbook: pre.yml

# All common things
- hosts: ansiblemanaged_True:&dev
  user: ansible
  become: True
  roles:
    - common


# All webservers
- hosts: ansiblemanaged_True:&dev_webservers
  user: ansible
  become: True
  roles:
    - webserver


# If the current host is not managed by Ansible
- hosts: ansiblemanaged_False:&dev
  user: ansible
  become: True
  tasks:
    - name: get last managed changed
      stat:
        path: /etc/ansible/facts.d/ansible_managed.fact
      register: factstat

    - name: all hosts not managed by Ansible
      debug:
        msg: |-
          Host *NOT* managed by Ansible.
            Host   : {{ inventory_hostname }}
            Reason : {{ ansible_local.ansible_managed.reason | default('Unknown') }}
            Date   : {{ '%Y-%m-%d %H:%M:%S' | strftime(factstat.stat.ctime) }}
```

The first play runs for all hosts that are a member of the
`ansiblemanaged_True` (under Ansible control) **and** `dev` group.

The second play show a host-selection for all hosts that are a member of
`ansiblemanaged_True` (under Ansible control) **and** are member of
`dev_webservers`.

The last play shows all hosts in the `dev` group that are member
of the dynamic group `ansiblemanaged_False`. This group has the
`ansible_local.ansible_managed.managed` fact set to `False` and are thus
(momentarily) out of Ansible control.
