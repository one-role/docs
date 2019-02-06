# Multiple environments

Ansible is not designed for use in a multiple environment situation, but
it can done.

When you have multiple development environments, like
a Develop-Test-Production set, the `setup` module can be used to
distinguish between these environments.

The `roles.yml` file contains all needed roles on the Ansible server,
but it also contains the version of this role.  This version can be
a release tag value, commit hash, or branch name. Defaults to `master`.

The `production` environment uses the `master` branch of all roles. The
`master` branches should therefore be locked, to make sure that no code
is deployed into production by accident.

Example `roles.yml`:

```yaml
- src: https://git.example.net/ansible/common.git
  scm: git
  version: 1.4.0
  name: common
```

When development and testing of the `1.4.0` version is done, this can be
taken into production by submitting a pull-request and merging with the
`master` branch.
