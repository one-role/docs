# Document the documentation

The complete Ansible tree is documented using the _MKDocs_ documentation
system. All documentation is done in MarkDown, with a bit of MKDocs flavour
added.

And as all documentation is in Markdown with the
[mkdocs](https://www.mkdocs.org/) flavour, the `mkdocs` package needs to
be installed.

```.none
pip install markdown
pip install mkdocs
pip install mkdocs-material
pip install pygments
pip install pymdown-extensions
```

## Theme and extensions

The used theme is the _Material_ theme
<https://squidfunk.github.io/mkdocs-material/> with the `pymdownx` and
`admonition` extensions installed.

## Generate

Every Ansible role has the complete documentation in it's own `README.md` file
and these `README.md` files are gathered with the `bin/refresh` script,
which is in the `docs` repository.

This way the documentation is in the role and changes are kept up to date.

## View

Viewing the documentation can be done in multiple ways, which are all web-based.

- Internal webserver
- External webserver

### Internal webserver

The `mkdocs` command contains an integrated webserver, that can be started
with:

```.none
mkdocs serve
```

Now you can point your browser to the documentation site:
[http://docserver:8000](http://docserver:8000)

### External webserver

When you already have a webserver, this can be used to display all documentation.
Of course you can create a separate virtual host or just host the documentation
in a separate directory.

The command

```.none
mkdocs build
```

creates a complete tree of all documentation in the `site` directory.

### With a git hook

In our case we automatically rebuild a static documentation site from
the complete MKDocs tree. This is done by a `post-merge` git hook,
containing:

```
#!/bin/bash

#  vi: set sw=4 ts=4 ai:

# Program      : post-merge
# Author       : Ton Kersten
# Date         : 27-07-2018

# This program is a git hook, that is run after a `git-pull`
# and it recreates the complete doc directory

# Determine the program name and the 'running directory'
IAM="${0##*/}"
CRD="$( [[ "$(printf "${0}" | cut -c 1 )" = "." ]] &&
    {   printf "${PWD}/${0}"
    } || {
        printf "${0}"
    })"
CRD="${CRD%/*}"
CUR="${PWD}"

TOPDIR="${CRD}/../.."
DOCDIR="/var/www/html/docs"

export PATH="/bin:/usr/bin:/sbin:/usr/sbin:/usr/local/bin"
cd ${TOPDIR}
/usr/bin/mkdocs build           \
    --clean                     \
    --quiet                     \
    --theme material            \
    --site-dir ${DOCDIR}        \
    --config-file ${TOPDIR}/mkdocs.yml
```

Now the docs can be reached through the standard webservice on:
`http:///docserver/docs`.

## Docs As A Service

To run MKDocs as a service create a _systemd_ service file in
`/etc/systemd/system/mkdocs.service` containing

```
[Unit]
Description=MKDocs
After=network.target

[Service]
Type=simple
User=root
Group=root
TimeoutStartSec=0
Restart=on-failure
RestartSec=10
RemainAfterExit=yes
WorkingDirectory=/etc/ansible/roles/dev/docs
ExecStart=/bin/mkdocs serve

[Install]
WantedBy=multi-user.target
```

And enable and start the service

```
systemctl daemon-reload
systemctl enable mkdocs
systemctl start mkdocs
```

And open the firewall

```
firewall-cmd --add-port=8000/tcp --permanent
firewall-cmd --reload
```

## Missing fonts

There is something weird with the `fontawesome-webfont` font package.
On CentOS the file `fontawesome-webfont.svg` is missing. This file
is placed in the `fonts` directory and should be copied to the
`/usr/share/fonts/fontawesome` directory on the server running MKDocs.
