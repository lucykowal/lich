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

additional services can be added easily via `config.yml`. for example:

```yaml
containers:
  - subdomain: subdomain # available via subdomain.luuucyyy.net
    name: container # name for container
    image: ghcr.io/lucykowal/repo:main # image to run
    container_port: "80" # image listens on port 80
    host_port: "8080" # container is available via localhost:8080
  - subdomain: another
    name: another
    image: ghcr.io/lucykowal/other:main
    container_port: "80"
    host_port: "8081"
```

the process is as follows:

1. build and deploy a docker image to ghcr
2. add an entry to `config.yml`. pull the above image, use the expected
   container port, use an unused host port, and pick a unique name and
   subdomain.
3. if this is the first time setting up this route, run the entire playbook with
   `ansible-playbook main.yml --vault-pass-file <secret file>`. if you only need
   to update the image, add the `--tags docker` filter.

both `subdomain` and `host_port` should be unique.
[health](https://health.luuucyyy.net) is taken.

### to-do

- [ ] automatically create DNS A records for subdomains
- [ ] support docker compose based services
- [ ] use github action to run playbook on merge/manual trigger/trigger by other
      action(s)
