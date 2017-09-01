# Ansible Role: Nginx

[![Build Status](https://travis-ci.org/geerlingguy/ansible-role-collectd-signalfx.svg?branch=master)](https://travis-ci.org/geerlingguy/ansible-role-collectd-signalfx)

Installs [SignalFx's fork of Collectd](https://github.com/signalfx/signalfx-collectd-installer) on Linux servers, and allows easy management of collectd integrations for SignalFx.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    signalfx_token: ''

Set your SignalFx token, otherwise collectd stats won't transmit!

    collectd_hostname: ''

Explicitly set a Hostname. If left empty, the server FQDN will be used instead.

    collectd_installer_url: https://dl.signalfx.com/collectd-install
    collectd_install_command: "/tmp/collectd-install {{ signalfx_token }} -y"
    collectd_bin_path: /usr/sbin/collectd

These settings are used to install SignalFx's forked Collectd version, and to verify it is installed (using the `collectd_bin_path`). Generally, you shouldn't need to change these.

    collectd_signalfx_managed_config:
      - url: https://raw.githubusercontent.com/signalfx/integrations/master/collectd-nginx/10-nginx.conf
        filename: 10-nginx.conf

Add list items with keys `url` and `filename` to download managed config files from a URL to the collectd managed_config directory. Adding a new item will trigger a restart of collectd at the end of the playbook run. For a listing of all the example configurations from SignalFx, check out the [`signalfx/integrations`](https://github.com/signalfx/integrations) repository.

    collectd_managed_config_changes:
      - filename: 10-nginx.conf
        regexp: '^  URL.+nginx_status"$'
        line: '  URL "http://localhost:81/nginx_status"'

Add list items with keys `filename`, `regexp`, and `line` to change managed config file lines. This is most useful if you just need to do something like change a port number or switch a flag. For anything more complex, you should copy your own template.

In the example above, the file `10-nginx.conf` will be scanned for a line matching the `regexp` (in this case, a line that starts with `  URL` and includes `nginx_status`). Then the entire line will be replaced with the contents of the `line` parameter. In this case, the change will set the Nginx server port where it's status is retrieved to `81`.

## Dependencies

None.

## Example Playbook

    - hosts: server
      roles:
        - geerlingguy.collectd-signalfx

## License

MIT / BSD

## Author Information

This role was created in 2017 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
