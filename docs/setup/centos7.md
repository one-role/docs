# Overview

The base system is a CentOS 7 machine, called
`ansible.prd.example.net` with IP address: `192.168.0.254`.

This machine has a multitude of functions:

- Ansible master host
- PXEboot (TFTP)
- RPM repositories (for CentOS, EPEL, local, Elastic?)

Notes:

- SELinux is _enabled_ and _Enforcing_


## Packages

Install these for your creature comforts on the shell and useful diagnostic tools.

```bash
yum -y install bash-completion nmap nmap-ncat tcpdump telnet tmux vim
```

Log out and log on again to make sure your shell loads new settings and
auto-completion scripts. Or source them yourself without logging out,
the choice is yours.


## Preparations to be made

1.  Add an extra disk to the host in VMWare (I added a 200G disk)
1.  Create a single partition on the new disk:

        parted -s /dev/sdb mklabel msdos mkpart primary xfs 2048s 100% set 1 lvm on

1.  Create a single PV, VG and LV that are going to store the repositories and other installation
    server required files:
    
        pvcreate /dev/sdb1
        vgcreate data /dev/sdb1
        lvcreate data --name deploy --size 100G

1.  XFS filesystem on the new LV:
        
        mkfs.xfs /dev/data/deploy

1.  Install Apache, CreateRepo, rsync and SELinux related utilities:
    
        yum -y install createrepo httpd rsync policycoreutils-python
 
1.  Create the needed directories and mount the new LV under `/var/www/html/deploy`:
    
        mkdir /var/www/html/deploy
        echo '/dev/mapper/data-deploy /var/www/html/deploy  xfs defaults    0 0' >> /etc/fstab
        mount -a
        
        df -hT /var/www/html/deploy
        Filesystem               Type  Size  Used Avail Use% Mounted on
        /dev/mapper/data-deploy  xfs   100G   33M  100G   1% /var/www/html/deploy

1.  Fix the SELinux permissions (required for Apache):
    
        semanage fcontext -a -t httpd_sys_content_t "/var/www/html/deploy(/.*)?"
        restorecon -R -v /var/www/html/deploy

1.  Prepare the repository mirror directories:
    
        mkdir -p /var/www/html/deploy/{dev,tst,acc,prd}
        mkdir -p /var/www/html/deploy/dev/repos/centos/7/{extras,os,updates}/x86_64/
        mkdir -p /var/www/html/deploy/dev/repos/epel/7/x86_64/

1. Mirror data from the online repositories:

    `RSYNC_PROXY` tells rsync to use the designated (Squid) proxy, which has to be configured to passthrough traffic over the rsync port (873 TCP).

        export RSYNC_PROXY=proxy.prd.example.net:3128
        rsync -avHS --delete                    rsync://mirror.nl.leaseweb.net/centos/7/extras/x86_64/  /var/www/html/deploy/dev/repos/centos/7/extras/x86_64/
        rsync -avHS --delete                    rsync://mirror.nl.leaseweb.net/centos/7/os/x86_64/      /var/www/html/deploy/dev/repos/centos/7/os/x86_64/
        rsync -avHS --delete                    rsync://mirror.nl.leaseweb.net/centos/7/updates/x86_64/ /var/www/html/deploy/dev/repos/centos/7/updates/x86_64/
        rsync -avHS --delete --exclude='debug*' rsync://mirror.nl.leaseweb.net/epel/7/x86_64/           /var/www/html/deploy/dev/repos/epel/7/x86_64/

# Serve the repositories to the network

The local administrators prefer to use Apache, so while nginx would be
very suitable here I'm not going to use it. Besides... there is a wish
to deploy ARA in order to monitor the Ansible playbook runs. ARA has
some documentation on how to use Apache+WSGI gateway instead of it's
built-in webserver to serve it's pages. So choosing Apache now may be
beneficial in the long run.

## Install Apache

        yum -y install httpd

## Configure Apache

Let's create a new VirtualHost config file in the Apache configuration
directory:

```bash
vim /etc/httpd/conf.d/ansible.prd.example.net.conf
```

```apache
<VirtualHost *:80>
    ServerName ansible.prd.example.net
    DocumentRoot /var/www/html

    ErrorLog /var/log/httpd/ansible.prd.example.net-error.log
    CustomLog /var/log/httpd/ansible.prd.example.net-access.log combined

    # Serve RPM and ISO files with MIME Content-Type: application/octet-stream
    AddType application/octet-stream .iso
    AddType application/octet-stream .rpm

    # Enable KeepAlives for speedier downloads
    KeepAlive On
    KeepAliveTimeout 3
    MaxKeepAliveRequests 100

    # Disable caching of these file types
    <LocationMatch "\.(xml|xml\.gz|xml\.asc|sqlite)$">
       Header set Cache-Control "must-revalidate"
       ExpiresActive On
       ExpiresDefault "now"
   </LocationMatch>
</VirtualHost>
```

## Enable Apache

```bash
systemctl enable --now httpd.service
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
```

Test if the directories can be reached:
URL: [http://ansible.prd.example.net/deploy/](http://ansible.prd.example.net/deploy/)


# Network (PXE) booting

Requirements:

- DHCP server on the network (present on `proxy.prd.example.net`)
- PXE bootloader, to select what you want to boot (manual install, auto install, others...)
- TFTP server, used to serve the Linux kernel and an initrd
- Image/System files to boot

### TFTP server

Pretty easy to install and get running, no configuration needed:

```
yum -y install tftp-server
systemctl enable --now tftp.socket
firewall-cmd --add-service=tftp --permanent
firewall-cmd --reload
```

### PXE bootloader

One is available in a useful package on CentOS 7:

```bash
yum -y install syslinux-tftpboot
```

### DHCP server

This can be a DHCP server of your choosing, as long as it supports
presenting it's clients with instructions where to find a PXE
bootloader. In our case I used what was already in use on
`proxy.prd.example.net`, which is the ISC `dhcpd`. For this piece of
software I had to add this to the configuration file
(`/etc/dhcp/dhcpd.conf`) on `proxy.prd.example.net`:

```
# Additional configuration for PXE booting
allow booting;
allow bootp;
option option-128 code 128 = string;
option option-129 code 129 = text;
next-server 172.19.114.254;
 filename "/pxelinux.0";
```

With `172.19.114.254` being the IP address of `ansible.prd.example.net`.
The filename is what the TFTP server on `ansible.prd.example.net` serves
from location `/var/lib/tftpboot/pxelinux.0`, owned by the package we
just install above (`syslinux-tftpboot`). And don't forget to restart
DHCPd to load the changed configuration:

```bash
service dhcpd restart
```

### CentOS 7 images

There are multiple ways to get the CentOS files in the right spot. You
can grab the files off a CentOS mirror from the `images/pxeboot`
directory of the release, or from a CentOS DVD which is what I did.

Insert/Connect a CentOS ISO image to the Ansible master machine/VM and
mount it:

```bash
mount /dev/sr0 /mnt/
```

Now copy the Linux kernel and the initrd image to the TFTP server
location:

```bash
mkdir /var/lib/tftpboot/centos7
cp -p /mnt/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/centos7/
```

The rest of the files needed to install/deply CentOS come from the
repositories we made a mirror of. In the section below we instruct the
CentOS installer (Anaconda) where to find it's so called "stage2".


### PXE configuration

What's next is to create a configuration for the PXE bootloader. This is
the place where we inform the PXE bootloader what kernel and initrd to
load for each menu option. We can also provide arguments for the Linux
kernel and initrd, such as an argument to inform the CentOS installer
where to find it's stage2 files and a kickstart script to use. For
example:

```bash
mkdir /var/lib/tftpboot/pxelinux.cfg
vi /var/lib/tftpboot/pxelinux.cfg/default
```

```
default vesamenu.c32
timeout 300
ontimeout local

menu clear

label linux
  menu label ^Manual Install CentOS 7 (from DEV repositories)
  kernel centos7/vmlinuz
  append initrd=centos7/initrd.img inst.stage2=http://ansible.prd.example.net/deploy/dev/repos/centos/7/os/x86_64 quiet net.ifnames=0 biosdevname=0 ks=http://ansible.prd.example.net/deploy/dev/kickstart/ks_c7_manual.cfg

label autolinux-dev
  menu label ^DEV :: Automatic Install CentOS 7
  kernel centos7/vmlinuz
  append initrd=centos7/initrd.img inst.stage2=http://ansible.prd.example.net/deploy/dev/repos/centos/7/os/x86_64 quiet net.ifnames=0 biosdevname=0 ks=http://ansible.prd.example.net/deploy/dev/kickstart/ks_c7_auto.cfg

label autolinux-acc
  menu label ^ACC :: Automatic Install CentOS 7
  kernel centos7/vmlinuz
  append initrd=centos7/initrd.img inst.stage2=http://ansible.prd.example.net/deploy/acc/repos/centos/7/os/x86_64 quiet net.ifnames=0 biosdevname=0 ks=http://ansible.prd.example.net/deploy/acc/kickstart/ks_c7_auto.cfg

label autolinux-prd
  menu label ^PRD :: Automatic Install CentOS 7
  kernel centos7/vmlinuz
  append initrd=centos7/initrd.img inst.stage2=http://ansible.prd.example.net/deploy/prd/repos/centos/7/os/x86_64 quiet net.ifnames=0 biosdevname=0 ks=http://ansible.prd.example.net/deploy/prd/kickstart/ks_c7_auto.cfg

label local
  menu default
  menu label Boot from ^local drive
  localboot 0xffff
```

We could also create submenus to hold various options. For example
a submenu for other environments or one for useful tools should we have
need for one (think of CloneZilla, ALT Linux Rescue, Memtest86). And
notice this configuration is set up to boot from local disk after 30
seconds automatically.

Test your deployment by trying to boot a (virtual) machine over the
network.
