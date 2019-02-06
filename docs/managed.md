# System management

All systems are managed with Ansible, in principle, but it is sometimes
necessary to take a machine out of Ansible control.

## Determine management

With every Ansible run the `playbooks/pre.yml` is run, which creates
a bunch dynamic groups and also creates a local fact indicating if the
machine is under Ansible control or not.

The fact group is called `ansible_managed` and contains two fields:
`managed` which is either `True` of `False` and `reason` which states
the reason of the managed status.

The `pre.yml` playbook uses these facts to create two dynamic groups, called
`ansiblemanaged_True` and `ansiblemanaged_False`, which can be used in
host selection.

When a host is found to be a member of the `ànsiblemanaged_False` group,
the master playbooks automatically give a message looking like:

```
ok: [web1] =>
  msg: |-
    Host *NOT* managed by Ansible.
      Host   : web1
      Reason : Reboot because of upgrade
      Date   : 2018-07-10 08:59:57
```

## Switching management on or off

To switch a system into unmanaged mode, or back again, setting the
host-variable `ansible_managed` for a system is needed. For example the
above host `web1`.

The `host_varѕ` file for host `web1` contains a couple of configuration
parameters that control the managed state of the system.

```
ansible_managed: False
reason: Reboot because of upgrade
```

This switches the managed state to `False` and the reason is displayed
in the playbook run. When the managed state is `True` the system is
managed with Ansible and the reason is ignored.
