# Ansible vhosts role

Role to make sure that vhosts works

## Requirements

The `apache role`

## Example Playbook

```
---
# Example playbook

# Set all dynamic groups
- import_playbook: playbooks/pre.yml

# All common things
- hosts: ansiblemanaged_True
  roles:
    - apache
    - vhosts
```

### Add sites:

Site definition matches close too:

!!! warning
    The `servername` or `ssl_servername` should be defined for a vhost!

```yaml
vhosts_remove_sites:
  - site1
  - site2
vhosts_sites:
  www.local.dev:
    servername: "www.local.dev"
    serveralias: "local.dev"
    listen_ip: '*'
    listen_port: '80'
    documentroot: "/var/www/html"
    headers:
      - unset "X-Powered-By"
      - merge Cache-Control no-cache
    proxy_pass:
      - { path: '/root1',  url: 'http://127.0.0.1:8080/root1' }
      - { path: '/root2',  url: 'http://127.0.0.1:8080/root2' }
      - { path: '/',       url: 'http://127.0.0.1:8080/' }
    redirect:
      status: 'permanent'
      src: '/rest'
      dest: '/meteo-rest-2.1'
    redirectmatch:
      status: 'permanent'
      regexp: '^/$'
      dest: '/root1'
    extra_parameters: |
      RewriteCond %{HTTP_HOST} !^www\. [NC]
      RewriteRule ^(.*)$ http://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
    ssl_servername: "www.local.dev"
    ssl_serveralias: "local.dev"
    ssl_listen_ip: '*'
    ssl_listen_port: 443
    ssl_documentroot: "/var/www/html"
    ssl_certfile: /path/to/cert/file
    ssl_keyfile: /path/to/key/file
    ssl_extra_parameters: |
      RewriteCond %{HTTP_HOST} !^www\. [NC]
      RewriteRule ^(.*)$ http://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
    ssl_proxy_pass:
      - { path: '/root1',  url: 'http://127.0.0.1:8080/root1' }
      - { path: '/root2',  url: 'http://127.0.0.1:8080/root2' }
      - { path: '/',       url: 'http://127.0.0.1:8080/' }
    ssl_redirect:
      status: 'permanent'
      src: '/old_url'
      dest: '/url'
    ssl_redirectmatch:
      status: 'permanent'
      regexp: '^/$'
      dest: '/root1'
```

This is a dictionary where every key is the name of the vhost.

!!! info
    All variables for _non-ssl_ do exist for _ssl_ as wel. For _ssl_
    the name of the variable is preceded by `ssl_`.\
    e.g. `proxy_pass` and `ssl_proxy_pass`

!!! warning
    The `require` option has a default of `all granted`, but this can be
    be overruled. When setting the `require` option to `False`, the
    `Require` will not be configured in the vhosts file.

## License
MIT


## Author Information
  - Stefan Joosten <stefan@atcomputing.nl>
  - Ton Kersten <tonk@smartowl.nl>


## Changelog
  - 2018-12-11 - Ton Kersten    - Added `SSLCertificateChainFile` option
  - 2018-11-13 - Ton Kersten    - Added skip `require` option to template
  - 2018-11-05 - Ton Kersten    - Added (ssl_)extra_docroot_parameters
  - 2018-09-28 - Stefan Joosten - Fixed some issues and added Redirect feature
  - 2018-08-09 - Ton Kersten    - Initial checkin
