# What do the different repositories do?

In our Ansible infrastructure, there are quite a few repositories that
are used. One rules them all though: `setup`.

This `setup` repository contains a single `master` branch. The environments
`dev`, `acc` and `prd` are represented by similarly named playbooks. For example
the playbook for the `dev` branch is called `dev.yml`. There is no need for
environment branches in the `setup` repository.

Each playbook contains mostly the same code, but each is tailored to run
against the environment it's named after. The line starting with `hosts:`
selects the hosts that belong to each environment, as defined by the inventory.

The roles themselves are _NOT_ part of the `setup` repository. Instead,
there is a `roles.yml` file that controls which modules are downloaded.
This all is handled by the `ansible-galaxy` command.

`ansible-galaxy` seeks out and downloads the roles it's told by the `roles.yml`
 file within the `setup` repository.

Example of the `roles.yml` file:

```yaml
- src: https://git.example.net/ansible/common.git
  scm: git
  version: master
  name: common

- src: https://git.example.net/ansible/postfix.git
  scm: git
  version: 1.0.1
  name: postfix
```
