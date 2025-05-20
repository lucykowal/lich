# lich: lucy's infrastructure code home

single-node ansible-managed infrastructure for a
[tiny personal webserver](https://cv.luuucyyy.net). powered by
[openresty](https://openresty.org), [docker](https://www.docker.com), and
[ansible](https://docs.ansible.com).

### usage

first, set:

```
HCLOUD_TOKEN=<your hetzner API key>
```

this allows the hetzner dynamic inventory to discover your hosts.

then, run:

```shell
ansible-playbook main.yml --vault-pass-file path/to/secret
```
