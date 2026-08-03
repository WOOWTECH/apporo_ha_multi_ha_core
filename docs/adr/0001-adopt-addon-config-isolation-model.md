# Adopt the `addon_config` isolation model

Each add-on now maps `addon_config:rw` and runs Home Assistant with `--config /config`
(the per-add-on `/addon_configs/<slug>` share), instead of mapping the shared `config:rw`
and running HA in a `/config/apporo_ha_core_N` subdirectory. We chose this so every instance
gets a fully isolated, private config directory that cannot collide with the host's main HA
config or with sibling instances — matching the reference `woowtech_ha_multi_ha_core` layout.

## Consequences

- **Breaking change (data path moves).** Existing installs stored data under
  `/config/apporo_ha_core_N` on the shared config share; the new model reads from
  `/addon_configs/<slug>`. Old data is not migrated and will not be read. This is why the
  add-on version jumps to `2.3.0` and the README calls it out.
- Slugs and host ports (8124–8128) are unchanged, so this is an in-place upgrade of the same
  add-ons, not a new set.

## Considered alternatives

- **Keep the subdirectory model** (`config:rw` + `/config/apporo_ha_core_N`) — zero breakage,
  but diverges from the reference architecture and keeps instances entangled with the host's
  shared config share. Rejected in favour of clean isolation.
- **Switch plus an automatic migration** that moves `/config/apporo_ha_core_N` into the new
  share — safest for existing users, but materially more complex and error-prone. Rejected as
  out of scope for this upgrade.
