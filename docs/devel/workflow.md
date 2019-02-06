# Development work-flow

When developing roles there is a certain work-flow in place that needs
to be respected, otherwise things can go astray.

There are some points that need to be considered before you can start:

1. All in-house developed roles have a branch `prd` that is the
   production branch, a acceptance branch `acc` and a development branch
   called `dev`.
1. The `setup` repository (containing the `roles.yml`) has a branch
   `master` indicating the production environment in the Ansible
   environment.
1. The `prd` and `acc` branches should/cannot be pushed to directly. Changes
   should flow to these branches from the `dev` branch through the pull-request
   / review / merge process.

## Role development

### Improving existing roles

1. Ensure you have the latest code role you wish to edit by checking out or
   pulling the latest `dev` branch from the `git server`.
1. Optionally create a new branch, *not* called `prd`, `acc` or `dev`.
1. Develop your code and test against local Vagrant boxes or test
   servers.
1. If satisfied: (merge,) commit and push your code to `dev` branch on the
   `git` server.
1. When this is the final stage of your code, and it can go into
   acceptance/production, create a *pull-request* against the `acc` branch, on
   the `git` server and select at least one reviewer.
1. Keep an eye on your pull-request and try to follow up with any questions or
   remarks the reviewer might have.
1. After your *pull-request* has been accepted and merged you can remove any
   feature branch(es) you have created.

**Note**: You can run into a permission issue when trying to push changes you
made to a pre-existing role to the `git` server. The `pull` script supplied by
the `setup` role makes use of the `ansiget` user who has `read` permissions
only. To push changes you should make use of the SSH transport and the `git`
user. You could alter the remote URLs of each role you edit, or you edit your
 `.gitconfig` and add the following:

```
# Enforce SSH
[url "ssh://git@git.prd.example.net/"]
    insteadOf = https://ansiget:gettyget@git.prd.example.net/
```

This rewrites the remote HTTPS URI you get by default to a SSH transport and
uses the `git` user for all "remotes" that fit the string in the `insteadOf`
field.


### Developing new roles
New roles can be created to add functionality or improve granularity. 
The process is rather similar to improving and existing role with the added
steps than you will need to add your role to `roles.yml` and perform more
extensive testing as described in the next heading.

1. Create new role by creating the appropriate directory structure. A script
   is present to perform these tasks for you: A new role skeleton (directory
   tree, short `README.md` and git branches) can be created with the
   `bin/newrole <name>` command. 
1. Develop your role, and make sure you keep the 
   [Coding Style](http://ansible.prd.example.net/docs/devel/codingstyle/) in
   mind. 
1. Test your code often on a Vagrant box or local VM.
1. Ensure the `README.md` of your new role is complete and up-to-date.
1. When you are ready to start deployment and testing your creation in the
   real environment submit your code repository to the `git` server. Again
   keep the naming conventions in mind. 
1. Test your code by following the *Testing your changes on the development
   hosts* paragraph below. TODO: or follow the deployment instructions


### Testing your changes on the development hosts
When your code is ready to be deployed you can do so on the development
environment.

TODO: fix instructions below or point to deployment and update that!

1. As you are not allowed to deploy test code in production, it's required
   to have a separate environment for your code tests.
1. Go to the `setup` module and create a new branch, not being
   `prd`, `acc`, `dev` or `master`. This branch could be called something like
   the ticket-number for this changes, the developer of this change, or
   any other short, descriptive name.
1. Change the `roles.yml` to reflect your changed role(s), so that
   *Ansible Galaxy* will pick up the changes.\
   This means that the `version:` tag points to your new branch.
1. Commit and push this branch and test against the newly created
   environment.
1. To deploy to the `dev` environment, have a look at
   [deployment](deployment.md)

## Useful commands

After installing the Ansible development environment, some extra
commands become available:

`pull`
: Clone or refresh all *on-site* developed roles

`push`
: Push all *on-site* developed roles to the git server

`newrole <new_role>`
: Create a new role skeleton.
