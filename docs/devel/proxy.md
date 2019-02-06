# Using the proxy

Some, or all, hosts need proxy settings to connect to the Internet. As
Ansible runs most modules through it's own system and not through
a shell, like Bash, there has to be a way to tell Ansible to use the
proxy server.

In an Ansible playbook it's possible to add an `environment` tag,
setting environment variables for playbook modules to use.

```
---
- hosts: ansiblemanaged_True:&rsc_test
  user: ansible
  become: True

  environment:
    http_proxy: http://proxy.server.net:3128
    https_proxy: http://proxy.server.net:3128
    no_proxy: localhost,127.0.0.1,server.net,192.168.42.0/24

  roles:
    - apache
    - masters
```
