## Coding style

- YAML files - All `yaml` files should be named with a logical name and
  end with `.yml`
- Use 2 space indents and nothing else
- All YAML files should start with the (optional) `---` and should *not*
  end with the (also optional) `...`
- Variables - Use Jinja variable syntax over deprecated Ansible variable syntax.
  `{{ var }}` not `$var`
- Use spaces around Jinja variable names. `{{ var }}` not `{{var}}`
- Variables that are environment specific and that need to be overridden
  should be in `ALLCAPS`.
- Variables that are internal to the role should be lowercase.
- Keep roles self contained - Roles should avoid including tasks from
  other roles when possible
- Plays should do nothing more than include a list of roles except where
  `pre_tasks` and `post_tasks` are required (to manage a load balancer for
  example)
- Plays/Playbooks that apply to the general community should be copied
  to `playbooks`
- Plays/Playbooks that apply only to a specific organization
  (`example-east`, `example-west`) should be copied to a sub-directory
  under `playbooks`
- Separators - Use underscores (e.g. `my_role`) not dashes (`my-role`).
- Paths - When defining paths, do include trailing slashes when the path
  is a directory (e.g.  `my_path: /foo/` not `my_path: /foo`. When
  concatenating paths, follow the same convention
  (e.g. `{{ my_path }}/bar/` not `{{ my_path }}/bar`)

## Naming conventions

As almost all roles need variables that can bet set per group or per
host, a strict naming scheme is adhered.

If we take the example role named `foobar` then the file
`/etc/ansible/inventory/group_vars/<groupname>/foobar` needs to be
created and should contain something like:

```.none
foobar_variable1: value1
foobar_variable2: value2
foobar_variable3: value3
```

And these variables can be used in roles and templates like
`{{ foobar_variable1 }}`

## Task names

Task names should be short, descriptive and all lower case.
Also prepend the name of the task with the name of the role.
This seems a little over the top, but it improves debugging.

Something like this:

```.none
- name: common | ensure the mailserver is running
```

When a role contains more than one task file or handler file, this is
also shown in the task naming, like so:

```.none
- name: common:mail | ensure the mailserver is running
```

Where `mail.yml` is the taskfile where this task resides.

## Conditionals and Return Status

- Always use `when`: for conditionals - To check if a variable is defined
  `when: my_var is defined` or `when: my_var is not defined`
- To verify return status (see
  [conditionals](http://docs.ansible.com/ansible/latest/playbooks_conditionals.html))

```.none
- command: /bin/false
    register: my_result
    ignore_errors: True

- debug: msg="task failed"
  when: my_result is failed
```

## Formatting

Use yaml-style blocks.

Good:

```.none
- name: ensure file exists
  file:
    dest: "{{ test }}"
    src: ./foo.txt
    mode: 0770
    state: present
    user: root
    group: wheel
```

Bad:

```
- file: >
    dest={{ test }} src=./foo.txt mode=0770
    state=present user=root group=wheel
```

Break long lines using yaml line continuation

## Roles

### Role Variables

- `common` role - Contains tasks that apply to all roles that are
  company specific.
- Roles variables - Variables specific to a role should be defined in
  `.../group_vars/<groupname>/<rolename>`.
  All variables should be prefixed with the role name.
- Role defaults - Default variables should configure a role to install
  `<service>` in such away that all services needed can run on a single
  server
- Variables that are environment specific and that need to be overridden
  should be in all caps.
- Every role should have a standard set of role directories

```
  └── rolename
      ├── defaults
      ├── files
      ├── handlers
      ├── meta
      ├── tasks
      ├── templates
      └── vars
```

The unneeded directories can be left out, but the preferred way is to
keep them with an empty `.gitkeep` file.

### Role Naming Conventions

- Role names - Terse, one word if possible, use underscores if
  necessary.
- Role task names - Terse, descriptive, spaces are OK and should be
  prefixed with the role name.
