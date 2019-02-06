# Command overview

The complete Ansible tree is controlled by some non-standard commands,
determining what should happen. These commands are already mentioned in
the rest of the documentation, but for the sake of ease they a overview
is placed here.

## Setup module

The setup module contains the most commands, for the complete control of
the Ansible tree.

| Command       | Explanation
|:--------------|:------------------------------------------------------------------------|
| `pull`        | The `pull` command reads the file `roles.yml` and determines if the role already exists. If it does, `pull` does a `git pull` on this role, otherwise a `git` clone is performed on the role-url |
| `push`        | For all directories in the current directory, containing a `.git` directory, a `git push` is performed. |
| `ansible_run` | The main command to perform Ansible playbook runs. The command takes one, two ore more parameters to perform its actions   |
|               | 1. The parameters is the environment used for the run                   |
|               | 2. The first parameter is the playbook to run, the second the that will be used. |
|               | 3. The first parameter is the playbook to run, the second the that will be used. All extra parameters are directly passed to the underlying `ansible-playbook` command. |
| `enc_str`     | Encrypt a string with the vault password for usage in a variable file. The first parameter is the name of the variable, the rest is the string to encrypt |
| `ve`          | The `ve` command stands for _Vault Edit_ and can be used to edit Ansible vault files |
| `refresh`     | Refresh all installed roles, either our own, or the ones from the Galaxy. Only version updates or new installs will be performed. The `-f` flag enforces a complete reinstall of the role-tree |

## Documentation module

The Ansible documentation module uses all the `README.md` files from all the
roles to generate all role documentation.

| Command       | Explanation
|:--------------|:------------------------------------------------------------------------|
| `refresh`     | The `refesh` command collects all `README.md` files from all roles to complete the role documentation. After the refresh a `git add .; git commit; git push` should be performed. |
