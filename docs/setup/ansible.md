# Setting up Ansible

## Installation

The machine running Ansible is the _Ansible Control Node_, often called
the _Ansible Master_, although Ansible does not have the _Master
/ Slave_ concept.

This control node should be installed on a Linux machine, because this is the
only one supported, right now. In the examples a CentOS7 machine is used, but
the same theory goes for Ubuntu, Debian and other Linux flavors as well.

When running CentOS the `epel` repositories should be available, as Ansible
resides in this. And as all roles are kept in _git_ repositories, the `git`
command needs to be installed as well.

```.none
yum -y install epel-release
yum -y install libselinux-python
yum -y install git
yum -y install ansible
```

## Configuration

During installation of Ansible the directory `/etc/ansible` was created.
In here you will find a default configuration to start out with. As we
have a complete configuration ready, let's move the original directory
somewhere safe and checkout the Ansible configuration from the Git
server.

```bash
cd /etc
mv ansible ansible.original
git clone https://github.com/one-role/setup.git ansible
cd ansible
./refresh -f
```

## Ansible user

The Ansible command uses _ssh_ to login to a remote system and for that
the `ansible` user is used. This could be the `root` user as well, but
that is not very common. The easiest way to use the `ansible` user is
through the aid of SSH keys and configure Ansible to use these.

The configuration that was checked out is set to log on and run as user
`ansible`, relying on SSH keys to log in at the client side. This user
is added to the client automatically when a new machine is deployed with
the kickstart file, but we need to create this user ourselves on our
Ansible control node.

```bash
useradd -c 'Ansible user' -d /home/ansible ansible
su - ansible
ssh-keygen -t rsa -C 'Ansible key'
cd .ssh
cp -p id_rsa.pub authorized_keys
exit
```

We added a user called `ansible`, logged in as that user, created SSH
keys and added the public key to the `authorized_keys` file so Ansible
can now log in to `localhost` as well.
