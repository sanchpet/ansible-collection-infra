# sanchpet.infra — instructions for Claude Code

Public Ansible collection (`galaxy.yml`, namespace `sanchpet`, name `infra`). Roles live in `roles/`; consumers install it from git by tag.

## Rules

- **Public repo.** No hostnames, IPs, accounts, inventory, secrets or references to private repositories. A role knows *how* to configure a node, never *which* nodes or *whose* keys: those come from the consumer's inventory.
- **Variable names are a contract.** Renaming a role variable breaks every consumer silently (the old variable is just ignored). Treat it as a breaking change.
- **Community first.** Where a maintained community role exists (devsec.hardening), wrap it and pin it in `galaxy.yml` dependencies; do not reimplement it.
- English for all repo artifacts. `--signoff` on every commit.
- **Conventional Commits + release-please.** PRs are squash-merged, so the PR title is the release commit (CI checks it). Never tag or edit `CHANGELOG.md` by hand; merge the release PR.
- Branch + PR.

## Checks

```sh
mise run deps && mise run lint && mise run build
```
