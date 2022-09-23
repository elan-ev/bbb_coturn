# Set up a TURN/STUN server for [BigBlueButton](https://bigbluebutton.org/)

![molecule](https://github.com/elan-ev/bbb_coturn/actions/workflows/molecule.yml/badge.svg)

This is an ansible-role to set up [coturn](https://github.com/coturn/coturn)
to work with [BigBlueButton](https://github.com/bigbluebutton/bigbluebutton)
and largely follows the [official BigBlueButton documentation](https://docs.bigbluebutton.org/admin/setup-turn-server.html).

## Dependencies

This role makes use of the `ansible.posix collection` (`ansible-galaxy collection install ansible.posix`).

## Role Variables

For a full overview of configuration options look at the [defaults](defaults/main.yml).
The default values all-in-all follow the official recommendations from the BigBlueButton docs.
However, you can configure some more options for extra security if you prefer.

### Custom Config Files

Instead of mapping a lot of variables from an ansible-config-file to a [coturn-config-file](https://github.com/coturn/coturn/blob/master/examples/etc/turnserver.conf),
you can simply specify the path to your own config-templates.
To do so, overwrite the default value in the variable `coturn_config_template`.

This way you can use this role for any coturn configuration that you might desire.

### Security and Firewall Related

This role can configure firewalld or ufw for coturn.
However, you have to tell it so explicitely by either setting `configure_for_firewalld` or `configure_for_ufw` to `true`.

### Additional Hardening

To prevent relaying traffic to other servers than your BigBlueButton nodes,
you can set `allowed_peer_ips` to the IPs of your BigBlueButton nodes
(or `allowed_peer_hosts` for the fqdns of the hosts to dig for them).

### TLS

By default, tls is configured and thus you should check if the default variables make sense for your setup.
If, e.g. for test purposes, you don't want to use tls, simply set the variable `use_tls` to `false`.

## Example Playbook

Your playbook might look like this:

```yaml
---

- hosts: all
  become: true
  roles:
    - role: elan.bbb_coturn
      static_auth_secret: 1234
      realm: foo.com
      configure_for_firewalld: true
      configure_logrotate: true

```

If you want to pass you own config template:

```yaml
---

- hosts: all
  become: true
  roles:
    - role: elan.bbb_coturn
      nginx_tls_config: 'my_templates/nginx_tls_config.yml.j2'

```

In this case you would have a configuration template for coturn that is located in a folder `my_templates` relative to the playbook.

## Development

For development and testing you can use [molecule](https://molecule.readthedocs.io/en/latest/).
With podman as driver you can install it like this – preferably in a virtual environment:

```bash
pip install -r .dev_requirements.txt
```

Then you can *create* the test instances, apply the ansible config (*converge*) and *destroy* the test instances with these commands:

```bash
molecule create
molecule converge
molecule destroy
```

If you want to inspect a running test instance use `molecule login --host <instance_name>`, where you replace `<instance_name>` with the desired value.

## License

[BSD-3-Clause](LICENSE)

## Author Information

[ELAN e.V](https://elan-ev.de/)
