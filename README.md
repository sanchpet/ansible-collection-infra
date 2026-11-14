# sanchpet.infra

Ansible collection that takes a fresh Debian VPS to a managed, hardened node: day-0 access, an OS baseline and CIS-style hardening. Written against SpaceWeb Debian images, works on any Debian host.

## Install

Straight from git, pinned to a release tag:

```yaml
# requirements.yml
collections:
  - name: https://github.com/sanchpet/ansible-collection-infra.git
    type: git
    version: v0.1.0
```

```sh
ansible-galaxy collection install -r requirements.yml
```

`ansible.posix` and `devsec.hardening` are pulled in as dependencies.

## Roles

| Role | What it does | Variables |
|---|---|---|
| `sanchpet.infra.bootstrap` | Day-0, run once as root: creates the admin user with SSH keys and passwordless sudo, then disables password auth and root login and locks the root password. | `bootstrap_admin_user` (default `admin`), `ssh_authorized_keys` (required) |
| `sanchpet.infra.common` | Baseline: hostname = inventory name, admin user's `authorized_keys` reconciled to exactly `ssh_authorized_keys`, packages, timezone, journald size cap, unattended-upgrades. | `bootstrap_admin_user`, `ssh_authorized_keys`, `common_timezone`, `common_packages`, `common_journal_max_use`, `common_journal_keep_free` |
| `sanchpet.infra.hardening` | `devsec.hardening` OS and SSH hardening with IP forwarding kept on (k3s and WireGuard need it), auditd log rotation, fail2ban. | devsec variables |
| `sanchpet.infra.private_network` | Netplan drop-in for a SpaceWeb private NIC, matched by MAC, no default route. | `private_network_ip`, `private_network_mac`, `private_network_prefix` |
| `sanchpet.infra.mesh_service_bridge` | socat systemd units relaying ports from an overlay address to targets only this host can reach. | `mesh_service_bridge_forwards` |

`bootstrap` and `common` share `bootstrap_admin_user` and `ssh_authorized_keys`, so set them once at inventory or play level.

## Example

```yaml
# bootstrap.yml — once per fresh host
- hosts: all
  become: true
  roles:
    - sanchpet.infra.bootstrap
```

```sh
ansible-playbook bootstrap.yml -u root   # add -k if the host only takes a password
```

```yaml
# baseline.yml — idempotent, run any time
- hosts: all
  become: true
  roles:
    - role: sanchpet.infra.common
      tags: [common]
    - role: sanchpet.infra.hardening
      tags: [hardening]
```

## Development

```sh
mise install && mise run deps
mise run lint
mise run build
```

## License

MIT
