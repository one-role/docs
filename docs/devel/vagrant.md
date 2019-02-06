# Vagrant

For local development of Ansible roles, a Vagrant setup is available.
This setup can facilitate two CentOS7 machines called `master` and
`client1`.

## Installation

In this setup Vagrant is used with the (default) Virtual Box
provisioner, so that needs to be installed first.

Some Linux distributions have a Virtual Box version in their
repositories, but these a mostly very old. The best way is to download
a Virtual Box version directly from Oracle.
[here](https://www.virtualbox.org/wiki/Linux_Downloads)

The same goes for Vagrant, the best way to install Vagrant is to
download the latest version from HashiCorp.
[here](https://www.vagrantup.com/downloads.html).

## How to use

To manage the Vagrant machines, clone the `vagrant` repository and enter
the `vagrant` directory. Now all Vagrant commands can be used.

Some examples:

- `vagrant up` : Start all Vagrant boxes
- `vagrant up master` : Start the `master` Vagrant box
- `vagrant halt` : All Vagrant boxes
- `vagrant halt master` : Stop the `master` Vagrant box
- `vagrant destroy` : Remove the Vagrant boxes, for recreation
- `vagrant ssh master` : Start a `ssh` session into `master`

Some extra information can be found at Scott Lowe's blog
[here](https://blog.scottlowe.org/2014/09/12/a-quick-introduction-to-vagrant/)
and
[here](https://blog.scottlowe.org/2017/12/06/using-vagrant-with-libvirt-on-fedora/)

## Local mounts

The Vagrant directory contains an `ansible` directory, which is mounted
onto the `/etc/directory` of the `master` machine. In this directory
local development can take place.

## Build your own

Building a Vagrant box can be a painstaking and tedious job. To make
life easier a starting point is supplied.

In the repository directory on the repository server a Virtual Box
appliance is available. This file is called `Vagrant_Master.ova`.
Download this `ova` file and import it in Virtual Box.

The resulting virtual machine can now be changed and tweaked and when
you are happy a new Vagrant box can be build.

Package the VM to a Vagrant box with:

```
vagrant package --base Vagrant_Master
mv package.box ansi_rhel7-vbox.box
```

And copy it to the repository server.
