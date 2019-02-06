# Software sources

## Repositories

The Ansible master serves as a repository mirror for most of the software.
One notable exception is the GitLab, for which the online repository is used.
At time of writing the Ansible master hosts the following repositories:

* [CentOS-7 - Base](http://ansible.prd.example.net/deploy/dev/repos/centos/7/os/x86_64/)
* [CentOS-7 - Extras](http://ansible.prd.example.net/deploy/dev/repos/centos/7/extras/x86_64/)
* [CentOS-7 - Updates](http://ansible.prd.example.net/deploy/dev/repos/centos/7/updates/x86_64/)
* [CentOS-7 - Epel](http://ansible.prd.example.net/deploy/dev/repos/epel/7/x86_64/)
* [Elasticsearch repository for 6.x packages](http://ansible.prd.example.net/deploy/elastic-6/)
* [Elasticsearch Curator](http://ansible.prd.example.net/deploy/dev/repos/local/curator-5)
* [PostgreSQL database ver.9.6](http://ansible.prd.example.net/deploy/dev/repos/local/pgdg96)
* [PostgreSQL database ver.10](http://ansible.prd.example.net/deploy/dev/repos/local/pgdg10)
* [Local](http://ansible.prd.example.net/deploy/dev/repos/local/)


All of these, except the local repository, are updated on a daily basis from
online sources. A task performed by `crontab` which in turn runs the
`/usr/local/bin/sync-repos.sh` script. Output of this script is sent to
file `/var/log/sync-repos.log` and overwritten each run. The
[`masters`](https://git.prd.example.net/ansible/masters) role is tasked with the
deployment of the script and creating the reponsible entries in the crontab of
the `root` user.

## Patch management

To exercise control over available package versions for the different
environments (_dev_ & _tst_, _acc_, _prd_) each makes use of their
own repository. Three such repositories have been created:

* [`dev` repository](http://ansible.prd.example.net/deploy/dev/repos); updated daily by the `sync-repos.sh` script
* [`tst` repository](http://ansible.prd.example.net/deploy/tst/repos); populated from `dev` by running script `dev2tst`
* [`acc` repository](http://ansible.prd.example.net/deploy/acc/repos); populated from `tst` by running script `tst2acc`
* [`prd` repository](http://ansible.prd.example.net/deploy/prd/repos); populated from `acc` by running script `acc2prd`

The CentOS package manager `yum` has been configured to use these repositories
depending on the environment the machine is in. With the `dev` repository being
updated daily any patches can be installed on the development hosts at any
time. The most recent software should be available within 24h after the online
repositories have received their updates.

### Updating dev, tst and acc

We recommend updating the `dev` environment frequently to limit the amount of
changes the updates may cause and testing can be completed swiftly. After
having successfully updated the `dev` environment you can proceed by updating
the `tst` and `acc` software repositories. This is performed as follows:

1. Log into the `ansible.prd.example.net` host
2. Acquire `root` privileges
3. Change directory to `/var/www/html/deploy/`
4. Run: `./dev2tst`

This script performs the following tasks:

* the `tst` repository and accompanying files are deleted
* the `dev` repository is copied (hard linked) to the `tst` repository
* accompanying `dev` files are also copied (hard linked) to the `tst` tree

The accompanying files may contain any files you also wish to manage
depending on the environment. For example extra scripts, separate .rpm files,
 Tomcat .war applications, etc. These files are available below URLs such as:
[http://ansible.prd.example.net/deploy/dev/files/](http://ansible.prd.example.net/deploy/dev/files/)

The packages available for installation in the `tst` environment will now
match those available within the `dev` environment. Of course only until the
next run the `sync-repos.sh` script is performed updating the `dev`
repositories. The `tst` repository content will remain the same until you run
the `dev2tst` script again.

Subsequently you can run the `./tst2acc` script to copy the `tst`
repositories to the `acc` environment.

### Updating prd

The process of updating the `prd` environment is largely similar to updating
the `acc` environment. The tasks performed are:

1. Log into the `ansible.prd.example.net` host
2. Acquire `root` privileges
3. Change directory to `/var/www/html/deploy/`
4. Run: `./acc2prd`

This script performs tasks similar to the `dev2tst` script with one change:

* repository and files in the `prd-org` directory are removed
* the `prd` repository and accompanying files are *moved* to `prd-org`
* the `acc` repository is copied (hard linked) to the `prd` repository
* extra `acc` files are also copied (hard linked) to the `prd` tree

The `prd-org` directory is created to allow for rollback purposes. In case
updates to `prd` go awry you can remove `prd` directory and rename the
`prd-org` back to `prd`. Thereby restoring the previous `prd` repository
and contents thereof.

## The Updating

Updates can be installed the regular way using `yum`, as is the norm on CentOS
systems. To aid with the process of updating all the hosts a couple of
playbooks have been created. Playbook `playbooks/update/main.yml` is designed
to provide an overview of available updates. It will not make any changes to
the systems, except for fetching fresh metadata.

```
ansible]# ansible-playbook playbooks/update/main.yml
```

You can decide to update each system by hand or use the power of Ansible to
automate this process to a degree you are comfortable with. Playbook
`playbooks/update/update.yml` performs the task of installing available updates.
By default this playbook runs for all hosts (hostgroup `all`). You can use the
`--limit` argument to restrict the playbook's run to a specific hostgroup or a
single host. Let's update all the machines belonging to the `dev` group:

```
ansible]# ansible-playbook --limit dev playbooks/update/main.yml
```

Remember you still have to restart any services that received updates to ensure
you are running the patched version. And any kernel updates should be followed
up by a reboot of the system as well. This recommendation is not related to
Ansible but rather good practise to ensure all patches are applied and running.
And at the same time testing the ability of the system to return in a proper
state after a reboot.

## Rolling back

CentOS provides means to rollback updates with the `yum history` command. Take
note that the older versions of the package you wish to rollback to needs to be
available in either Yum's cache or the repositories. If either of these
conditions is not met, you may need to temporarily point Yum to a repository
still carrying the older version.

For the production environment older packages are kept around for a generation.
Remember the `acc2prd` creating a `prd-org` directory containing the former
contents `prd`? That copy becomes useful in this context. Whenever you need to
rollback (a host in) the production environment you can point yum towards the
`prd-org` repository temporarily, ensuring all older versions of the packages
are available.

Such a task could be performed by logging into the `prd` host you wish to
perform the rollback on, becoming the `root` user and applying the following
commands:

```
cd /etc/yum.repos.d/local/
sed -i 's/prd/prd-old/g' *
yum clean metadata
yum history list
# now lookup the transaction ID you wish to undo, usually the latest transaction
yum history undo <transaction ID>
# follow instructions and solve any issues that may arise.
```
