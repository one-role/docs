# Ansible ssh role
Role to make sure that the OpenSSH service works and additionally configure a 
restricted SFTP server when desired.

The most important variables are `ssh_config` and `sshd_config`. These are 
dictionaries (key-value pairs) containing the client, resp. server configuration.
Have a look at `defaults/main.yml` to see what is configured by default. The
`ssh_config` matches CentOS 7 defaults and `sshd_config` does so too with few
 extra's (such as `TCPKeepAlive yes`).

Additionally the `ssh_sftp_server` can be switched to `yes`. When doing so you
should populate the `ssh_sftp_users` list accordingly.

This role is designed for CentOS 7. It probably also functions for other
 Red Hat distributions such as Fedora.


## Variables

### OpenSSH client configuration
`ssh_config` is used to create the configuration file for OpenSSH clients, 
usually saved at path `/etc/ssh/ssh_config`. It may contain other key-value
 pairs, named list(s) and one more level dictionary. Everything in here will
 end up in the configuration file. You should have a look at `man ssh_client`
 on your system to review all options. An example configuration below:

```
ssh_config:
  Host shelly:
    HostName: shell.example.com
    User: myusername
    Port: 1337

  Host *:
    ForwardX11Trusted: 'yes'
    GSSAPIAuthentication: 'yes'
    SendEnv:
      - 'LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES'
      - 'LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT'
      - 'LC_IDENTIFICATION LC_ALL LANGUAGE'
      - 'XMODIFIERS'
```

### OpenSSH server configuration
`sshd_config` is a dictionary used to write the OpenSSH server configuration
file, usually found at `/etc/ssh/sshd_config`. It uses the same mechanism as
the above `ssh_client`: it may contain key-value pairs, named list and one 
nested dictionary (which can again contain k,v pair(s) and named list(s)).

Example configuration (default as well):

```
sshd_config:
  AcceptEnv:
    - 'LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES'
    - 'LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT'
    - 'LC_IDENTIFICATION LC_ALL LANGUAGE'
    - 'XMODIFIERS'
  AuthorizedKeysFile: '.ssh/authorized_keys'
  ChallengeResponseAuthentication: 'no'
  GSSAPIAuthentication: 'yes'
  HostKey:
    - /etc/ssh/ssh_host_rsa_key
    - /etc/ssh/ssh_host_ecdsa_key
    - /etc/ssh/ssh_host_ed25519_key
  PasswordAuthentication: 'yes'
  PermitRootLogin: 'yes'
  PrintLastLog: 'yes'
  Subsystem: 'sftp /usr/libexec/openssh/sftp-server'
  SyslogFacility: AUTHPRIV
  TCPKeepAlive: 'yes'
  UsePAM: 'yes'
  UsePrivilegeSeparation: 'sandbox'
  X11Forwarding: 'yes'
```

### SFTP configuration
This role is capable of configuring an access restricted SFTP server on your
system using OpenSSH and local user accounts. If you are interested in this
option you should set `ssh_sftp_server` to `True`. This adds a section to the
OpenSSH server configuration (section defined in `ssh_sftp_config`) to restrict
user accounts that are member of group `sftponly` to their home directories.
OpenSSH chroot requires the home directories of such users are owned by 
`root:root` and having `rwxr-xr-x` permissions set. 

This role can also create restricted users for you, including special home
directories created below a directory defined in `ssh_sftp_homes` (default:
`/home/sftp`). Use a list structure to define the users the role should create
called `ssh_sftp_users`. When using LDAP and the users you create need to be
local you should set `ssh_sftp_local_users` to `yes`. This uses the `local`
option of the Ansible *user* module.

 An example of SFTP configuration:

```
ssh_sftp_server: yes

ssh_sftp_users:
  - username: user1
    password: "password1"
    name: "Nice name for user1"

  - username: user2
    password: $6$BZUWnYfNX1TwE/OD$S13HXY7T.lmkw.igAwJ54h6dRdPCpisEAOSlYUtJ5Z1eI5qjN2OgQ45Fpl1Kn0cDXMpbXH1YFacWJv1gPB/Vo1
    name: "User with a SHA512 hashed password"
    allow_upload: yes
```

This list may contain many of the options made available by the Ansible *user*
module. The options shown in the example above are sufficient in most cases.
An overview of all the options supported by this role (required keys in bold):

| Variable | Description |
|----------|-------------|
| `_username_` | The username of the user. Maps to Ansible *user*'s `user` option |
| `_password_` | Password for this user. Best practise is to store these in a hashed form.<br>The hashes can be created on the command line like so (Python >= 3.3, backported to some 2.7 (CentOS7 has it)):<br>`python -c 'import crypt,getpass; print(crypt.crypt(getpass.getpass(), crypt.mksalt(crypt.METHOD_SHA512)))'` |
| `comment` | Comment field, use this to add a human readable name.<br>This is also known as the GECOS field within `/etc/passwd`. |
| `expires` | An expiry time for the user in epoch |
| `groups` | Supplemental groups this user should be a member of.<br>Probably not that useful within SFTP chroot. |
| `state` | `present` if this user should exist on the system.<br>`absent` when it should not. (default: `present) |
| `uid` | User ID number to assign to this user. In case you wish to specify this manually.
| `update_password` | `always` will update passwords if they differ. <br>`on_create` will only set the password for newly created users.<br>Default set to `always`. |


## Idea(s) for future development
* Support use of SSH keys
* Support deletion of absent user's home directories?


## Example Playbook

```
---
# Example playbook

# Set all dynamic groups
- import_playbook: playbooks/pre.yml

# All common things
- hosts: ansiblemanaged_True
  roles:
    - ssh
  
  vars:
    sshd_config:
      AcceptEnv:
        - 'LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES'
        - 'LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT'
        - 'LC_IDENTIFICATION LC_ALL LANGUAGE'
        - 'XMODIFIERS'
      AuthorizedKeysFile: '.ssh/authorized_keys'
      ChallengeResponseAuthentication: 'no'
      GSSAPIAuthentication: 'no'
      PasswordAuthentication: 'no'
      PermitRootLogin: 'no'
      PubkeyAuthentication: 'yes'
      PrintLastLog: 'yes'
      Subsystem: 'sftp /usr/libexec/openssh/sftp-server'
      SyslogFacility: AUTHPRIV
      TCPKeepAlive: 'yes'
      UsePAM: 'yes'
      UsePrivilegeSeparation: 'sandbox'
      X11Forwarding: 'yes'   
```

## License
MIT


## Author Information
  - Stefan Joosten <stefan@atcomputing.nl>


## Changelog
  - 2019-01-17 - Stefan Joosten - update documentation
  - 2019-01-16 - Stefan Joosten - add SFTP configuration
  - 2019-01-15 - Stefan Joosten - Initial commit
