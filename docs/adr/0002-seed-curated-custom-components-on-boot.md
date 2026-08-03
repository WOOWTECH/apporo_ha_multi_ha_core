# Seed curated custom components on boot

Each add-on image bakes a curated set of 28 custom components into
`/opt/apporo_seed/custom_components` (via `COPY rootfs /`) and runs `apporo_seed.sh` as its
entrypoint. On boot the script copies each component into `/config/custom_components`
**only if that folder is absent** ("seed-if-missing"), then execs Home Assistant. We chose
build-time baking + boot-time seed-if-missing (rather than HACS-managed installs or
committing components directly into a live config) so the components ship pinned and offline,
every fresh instance starts identical, and user edits or removals are never overwritten on
restart.

## Consequences

- The 28 components are vendored under `_seed/custom_components` (the canonical source, pinned
  by `_seed/seed-manifest.tsv` / `seed-lock.tsv`) and materialised into all five add-ons'
  `rootfs/opt/apporo_seed/` by `scripts/sync-seed.sh`. The same tree is therefore stored six
  times in the repo — an accepted cost of HA's per-add-on rootfs overlay model.
- Only `custom_components` are seeded. No `configuration.yaml` is seeded; Home Assistant
  generates its own default on first boot, which is functionally equivalent and brand-neutral.
- `woow_zha_quirks` is seeded but must be enabled by the user; it is deliberately not
  auto-enabled because its heal task blocks startup on a ZHA-less instance.

## Considered alternatives

- **HACS-managed installs** — components would download at runtime, requiring network access
  and drifting per instance. Rejected; we want pinned, offline, identical seeds. (HACS itself
  is one of the seeded components, so users can still add more.)
- **Commit components straight into a live config dir** — no seed indirection, but then user
  removals get clobbered on every rebuild and there is no clean "fresh instance" story.
