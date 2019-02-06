# Documentation, add-on and Caveat

The Ansible Setup repository is the controlling repository of the
complete Ansible development tree.
This is the 'One role to rule them all'.

## How to install this thing?

To use this repository you need to install some tools so that all
functionality can be used.

Start with cloning this repository from the master _*Git*_ server:

```bash
git clone ssh://git@git.example.net:7999/ansible/setup.git setup
```

If this gives a `Unable to communicate securely with peer: requested
domain name does not match the server's certificate.`, make sure the
file `~/.gitconfig` contains:

```
[http]
sslVerify=false
```

Next download all the locally developed roles with the `pull` command.

```bash
setup/bin/pull
```

**Caveat**: This script uses SSH keys for Git credentials so the access
needs to configured.


## Roles file

The Ansible server needs the `roles.yml` to collect all needed roles,
through *Ansible Galaxy*.

## Setting up

* [Setup a development environment on Redhat](../devel/setup_redhat.md)
* [Setup a development environment on Ubuntu](../devel/setup_ubuntu.md)
* [Setup Visual Code Editor](../devel/editors/vscode.md)
* [Setup Atom Editor](../devel/editors/atomeditor.md)

## Explaining the infrastructure

* [What does each repo do?](repos.md)
* [Environments](environments.md)

## Workflow

* [Workflow](../devel/workflow.md)
