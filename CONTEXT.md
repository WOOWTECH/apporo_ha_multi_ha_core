# Apporo Multi HA Core Add-ons

A Home Assistant add-on repository that runs multiple isolated Home Assistant Core instances
on one host. Each add-on ships a curated set of custom components seeded into its private
config directory on first boot.

## Language

**Instance**:
One of the five add-ons (`apporo_ha_core_1`–`5`), each an independent Home Assistant Core with
its own config and host port (8124–8128).
_Avoid_: container, core (unqualified), node

**Seed**:
The act of copying a curated custom component into an instance's `/config/custom_components`
on boot. Governed by the seed-if-missing policy.
_Avoid_: install, provision, bootstrap

**Seed-if-missing**:
The rule that a component is only copied when its target folder is absent, so existing folders
and user edits or removals are never overwritten. There are two exceptions: an unused old
**component group** (ADR 0003), and a `hacs` folder without `hacs_frontend/`, which cannot
load and so cannot be in use (ADR 0004).
_Avoid_: sync, overwrite, upgrade

**Component group**:
Old split components that upstream consolidated into one successor: permission
(`ha_permission_manager` 1.x, `ha_area_control`, `ha_label_control` → `ha_permission_manager`
2.x), records (`ha_finance`, `ha_asset_record`, `ha_health_record`, `ha_note_record` →
`woow_ha_records`), protocol (`woow_dmx`, `woow_knx`, `woow_modbus` → `woow_multi_protocol`).
_Avoid_: bundle, family

**In use** (of a component group):
At least one of: a config entry of an old domain, a `.storage` file named after an old domain,
a non-empty `/config/dmx`, `/config/knx` or `/config/modbus` (protocol group), or a top-level
`configuration.yaml` key that is an old domain. Once `ha_permission_manager` 2.x is present in
`custom_components`, the entry and `.storage` file it shares with 1.x no longer count. A group
that cannot be checked counts as in use.
An unused group's old folders are replaced on boot; a group in use is left alone.
_Avoid_: active, enabled, configured

**Canonical seed** (`_seed/`):
The single source-of-truth copy of the 21 curated components plus the seed script and manifest.
`scripts/sync-seed.sh` materialises it into each instance's `rootfs/opt/apporo_seed/`.
_Avoid_: master copy, template

**apporo_seed**:
The apporo-branded on-boot seeding mechanism: the baked directory (`/opt/apporo_seed`), its
entrypoint script (`apporo_seed.sh`), and its log prefix (`[apporo_seed]`).
_Avoid_: woow_seed

**addon_config model**:
The isolation model where each add-on maps `addon_config:rw` and runs HA in `/config` (its
private `/addon_configs/<slug>` share), rather than a subdirectory of the host's shared config.
_Avoid_: config-share model, subdirectory model
