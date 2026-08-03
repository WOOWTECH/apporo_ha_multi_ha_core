# Apporo Multi HA Core Add-ons

A Home Assistant add-on repository that lets you run **Home Assistant inside Home Assistant**. Each
add-on launches an independent Home Assistant Core instance with its own configuration directory, so
you can operate multiple isolated HA cores on a single host.

Based on [jgoakley/hassio-addons](https://github.com/jgoakley/hassio-addons).

## Add-ons & Port Mapping

This repository contains 5 add-ons. Each exposes its internal Home Assistant port (`8123`) on a
distinct host port in the range **8124–8128**:

| Add-on             | Instance | Host Port |
| ------------------ | -------- | --------- |
| `apporo_ha_core_1` | 1        | `8124`    |
| `apporo_ha_core_2` | 2        | `8125`    |
| `apporo_ha_core_3` | 3        | `8126`    |
| `apporo_ha_core_4` | 4        | `8127`    |
| `apporo_ha_core_5` | 5        | `8128`    |

Once an add-on is running, open `http://<your-host>:<port>` (e.g. `http://homeassistant.local:8124`
for instance 1) to reach that Home Assistant instance.

## How It Works

Each add-on:

- Runs a Home Assistant Core image (`ghcr.io/woowtech/apporo-ha:2026.7.2` on amd64, the official
  `ghcr.io/home-assistant/home-assistant:stable` on other architectures).
- Stores its configuration in its own private add-on config share (`addon_config`), mounted at
  `/config`, keeping each instance fully isolated from the host's Home Assistant and from the
  other instances.
- Comes with a curated set of custom components pre-installed. On first boot the add-on seeds
  them into `/config/custom_components` (seed-if-missing — it never overwrites folders you have
  already added or edited). See [ADR 0002](docs/adr/0002-seed-curated-custom-components-on-boot.md).
- Maps the container's `8123/tcp` port to a unique host port (8124–8128).

## Upgrading to 2.3.0 (breaking change)

Version `2.3.0` moves each instance from a sub-directory of the host's shared config
(`/config/apporo_ha_core_N`) to its own private `addon_config` share. Configuration created by
earlier versions under `/config/apporo_ha_core_N` is **not migrated** and will not be read by
the new version — treat upgraded instances as fresh, or copy old data across manually. See
[ADR 0001](docs/adr/0001-adopt-addon-config-isolation-model.md).

## Installation

1. In Home Assistant, go to **Settings → Add-ons → Add-on Store**.
2. Open the **⋮** menu (top right) → **Repositories**.
3. Add this repository URL:
   `https://github.com/WOOWTECH/apporo_ha_multi_ha_core`
4. The five **Apporo HA Core** add-ons appear in the store. Install the ones you need.
5. Start an add-on, then open its host port in your browser to complete onboarding for that instance.

## Credits

Based on [jgoakley/hassio-addons](https://github.com/jgoakley/hassio-addons).
