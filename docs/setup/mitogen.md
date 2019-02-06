# Ansible Mitogen

An extension to Ansible is included that implements connections over
Mitogen, replacing embedded shell invocations with pure-Python
equivalents invoked via highly efficient remote procedure calls to
persistent interpreters tunnelled over SSH. No changes are required to
target hosts.

## Installation

Installing Mitogen from latest release on PyPi

```
python2-pip
pip install mitogen
```

## Configuration

Modify `ansible.cfg`

```
[defaults]
strategy_plugins = /usr/lib/python2.7/site-packages/ansible_mitogen/plugins/strategy
strategy = mitogen_linear
```
