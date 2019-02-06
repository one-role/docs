# Secure vs. Insecure data a.k.a. The Vault

As a general policy we want to protect the following data:

- Private / Public keys
- Passwords, API keys

Please use common sense.

## Vault

Secure vars are set in vault files under the `.../group_vars/<group>` or
`.../host_vars/<host>` directory.  These files will be passed in when
the relevant `ansible-playbook` commands are run.  If you need a secure
variable defined, give it a name and use it in your playbooks like any
other variable.  The value should be set in the secure vault files of
the relevant host or group.

It is also possible to encrypt a single string and add that to a normal
variable file.

A way to encrypt a single string could be something like:

```.none
printf 'password'                            | \
    ansible-vault                            \
        encrypt_string                       \
        --vault-password-file=PasswordFile   \
        --stdin-name='mysql_root_password'
```

Which could result in

```.none
mysql_root_password: !vault |
    $ANSIBLE_VAULT;1.1;AES256
    62636237366532626436666664373736663233343737313062383364323330326632633337376335
    3766613139383531383030646530616138386235393434630a643231336538326431353461633331
    61633930333866653337366361383164623431343931633663306536646233353163666434353038
    6661353735383761320a663862646536303537393035353235303030353464313764323966353361
    3134
```

Do *not* use the `echo` command with the `encrypt_string`, as the `echo`
adds an extra newline, which then becomes part of the encrypted string.

The encryption password for the vault files is kept outside of this tree
and should *not* be included anywhere in this tree!

## Vault password

The Ansible vault password can be supplied on the commandline with every
Ansible run, but that is not very feasible, especially when running
Ansible from `cron`. But there should be **no** way that the vault
password ends up in a `git` repository, so it should always be kept
outside the `git`-trees.

Another rule that applies is that all files that are encrypted, or have
encrypted strings in them, should have the same password. This just
because Ansible can deal with one password at a time.

To make sure this trick works, a file can be placed in the home
directory of user `root` and it should be named `.AnsibleVaultPassword`.
It should contain a single line, containing the vault password.

If this file is detected by the `ansible_run` script, it is
automatically used as the vault password and the option is supplied to
the `ansible-playbook` command.
