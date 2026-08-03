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
and user edits or removals are never overwritten.
_Avoid_: sync, overwrite, upgrade

**Canonical seed** (`_seed/`):
The single source-of-truth copy of the 28 curated components plus the seed script and manifest.
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
