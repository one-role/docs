# Setup workenvironment

## Prerequisites

* Ubuntu machine or derivate (Fedora, CentOS)
* Internet access
* Git server account with permission to the Ansible repositories

## Install VirtualBox

* [Download the latest version](https://www.virtualbox.org/wiki/Downloads)
* For increased performance, also install the Extension pack

## Setup ssh keys for the Git server

* Generate ssh-keys (Only if not yet present)

```bash
ssh-keygen
cat ~/.ssh/id_rsa.pub
```

* Add key to Git

## Clone and setup the Ansible setup development (this) repository

```bash
cd ~
mkdir ansible
cd ansible
git clone ssh://git@git.example.net:7999/ansible/setup.git setup
setup/bin/pull
```
