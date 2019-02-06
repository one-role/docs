# Ansible lvm role

Role to create and modify the Linux Logical Volume Manager (LVM2). 
Also mounts the created volume and edits `/etc/fstab`.

This role cannot be used to remove existing PV/VG/LVs.


## Requirements

Nothing special.

## Variables

The role looks for variable `lvm_config`, which should be a dictionary. 
The contents of `lvm_config` should be other dictionaries, whose name should match the LV to create or utilize.
Values of this second dictionary describe the VG to use, any LV specific options and filesystem specific options.
Below is an overview of what this should look like, with the optional keys are surrounded by brackets:

```
lvm_config:
  lvname:           Name of the Logical Volume to create or modify
    vgname:           Volume group the Lv should be part of
    filesystem:       Filesystem for partitions which need to be formatted
    lvmount:          Where the partition should be mounted
    lvsize:           Size of the partition
   [pvs]:             Comma separated list of physical volumes (/dev/sda, /dev/sdb)
                      Required when a new VG should be created; runs pvcreate on them.

   [pv_options]:      Additional options to pass to `pvcreate` when creating the volume group. (default: none)
   [pesize]:          The size of the physical extent. (Default: 4) (unit is MB)

   [vg_options]:      Additional options to pass to `vgcreate` when creating the volume group. (default: none)

   [lv_options]:      Free-form options to be passed to the lvcreate command. (default: none)
   [shrink]:          Shrink LV if current size is higher than size requested (default: False)

   [fs_options]:      Options to pass to the `mkfs` command (default: none)
   [fstype]:          Filesystem type to be created (default: ext4)
   [resizefs]:        If yes, if the block device and filesytem size differ, grow the filesystem into the space. (default: yes)

   [mount_options]:   Mount options (for example `noatime` or `ro,noexec`) (default: none)
   [dump]:            Set the fifth field of `/etc/fstab`; the dump (see fstab(5)) field (default: 1)
   [passno]:          Set the sixth field of `/etc/fstab`; determines if the filesystem should be checked (default: 2)
```

Example variable settings:

```
---
lvm_config:
  data:
    vgname: VG01
    lvmount: /data
    lvsize: 40G
    pvs: /dev/sdb
    filesystem: xfs
    shrink: False
  opt:
    vgname: VG00
    lvmount: /opt
    lvsize: 80G
    filesystem: ext4
```

## Example Playbook

```
---
# Example playbook

# Set all dynamic groups
- import_playbook: playbooks/pre.yml

# All common things
- hosts: ansiblemanaged_True
  roles:
    - lvm
```

## License
MIT


## Author Information
  - Ton Kersten <tonk@smartowl.nl>
  - Stefan Joosten <stefan@atcomputing.nl>


## Changelog
  - 2018-09-05 - Stefan Joosten - Multiple changes:
    - Replace ending of the play when lvm variable is unset by skipping the tasks
    - Allow creation of PV and VG from scratch
    - Expand configurable options
    - Create ext4 filesystem by default
    - Ensure required variables are present
    - Rename `lvfilesystem` variable to `filesystem`
  - 2018-07-30 - Ton Kersten - Initial checkin
