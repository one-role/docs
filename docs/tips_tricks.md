# Tips and tricks

This chapter contains all kind of Ansible tips and tricks

## Show all variables for a host
### Command line variant without `ansible_facts`:

```
ansible
    --vault-password-file=/root/.AnsibleVaultPassword
    -m debug
        -a "var=hostvars[inventory_hostname]" <host>
```

### Playbook variant with `ansible_facts`:
`playbooks/get_vars.yml`:
```
- hosts: all
  tasks:
    - name: Display all variables/facts known for a host
      debug:
        var: "{{ hostvars[inventory_hostname] }}"
```

Run with, while replacing `localhost` with the host you want the information of:

```
# ansible-playbook
    --vault-password-file=/root/.AnsibleVaultPassword
    get_vars.yml
    --limit localhost
```


## Encrypt a string for the vault

```
printf "str_to_encrypt"             | \
    ansible-vault                   \
      encrypt_string                \
      --stdin-name="variable_name"  \
      --vault-password-file=/root/.AnsibleVaultPassword
```

## Use fileglob with templates

```
- name: dns | ensure dns zone configuration
  template:
    src: "{{ item }}"
    dest: "/etc/named/zones/{{ item | basename | regex_replace('.j2$', '') }}"
    owner: root
    group: named
    mode: 0640
  loop: '{{ query("fileglob", "templates/zones/*.j2") }}'
  notify: restart_dns
```

## Show the content of a template

```
---
#
# This playbook renders a template and shows the results
# Run this playbook with:
#
#       ansible-playbook -e templ=<name of the template> template_test.yml
#
- hosts: localhost
  become: false
  connection: local

  tasks:
    - fail:
        msg: "Bailing out. The play requires a template name (templ=...)"
      when: templ is undefined

    - name: show templating results
      debug:
        msg: "{{ lookup('template', templ) }}"
```

## Gitlab

### Import of bare repositories

There are a couple of steps to this.

First create a directory to import from, which should be owned by user
and group `git`:

```
sudo -u git mkdir /var/opt/gitlab/git-data/repository-import-$(date '+%Y-%m-%d')
```

After creating the directory, copy all _bare_ repositories to this
directory and run:

```
sudo gitlab-rake gitlab:import:repos["/var/opt/gitlab/git-data/repository-import-$(date '+%Y-%m-%d')"]
```

### Reconfigure Gitlab

The configuration of Gitlab is in `/etc/gitlab/gitlab.rb` and after
changing settings Gitlab needs to be reconfigured.

```
gitlab-ctl reconfigure
```

### Create Gitlab backup

Gitlab has a built-in tool to create backups. This tool creates
a tar-archive in the directory `/opt/gitlab/backups` called
`eeeeeeeeee_2018_09_27_11.3.0_gitlab_backup.tar` where `eeeeeeeee` are
the seconds since _epoch_, `2018_09_27` is the currect date and `11.3.0`
is the current Gitlab version.

Command:

```
gitlab-rake gitlab:backup:create
```
