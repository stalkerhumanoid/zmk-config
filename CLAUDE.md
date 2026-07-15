# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

ZMK firmware configuration for a Chocofi split keyboard (Corne-compatible, 36 keys) running on nice!nano v2 controllers with nice!view displays. There is no application code — only devicetree keymap files, Kconfig settings, and build metadata consumed by ZMK's build system.

## Building

There is no local build. Firmware is built by GitHub Actions on every push using ZMK's shared `build-user-config.yml` workflow (pinned to `v0.3`, matching the ZMK revision in `config/west.yml`). Download `.uf2` firmware artifacts from the Actions run.

`build.yaml` defines the build matrix: left/right halves (`corne_left`/`corne_right` shields with `nice_view_adapter nice_view`) plus a `settings_reset` firmware.

## Key files

- `config/corne.keymap` — the keymap (devicetree syntax). This is where nearly all changes happen.
- `config/corne.conf` — Kconfig options (keyboard name, display enabled).
- `config/west.yml` — pins the ZMK firmware version (`v0.3`). Bump the `revision` here and the workflow tag in `.github/workflows/build.yml` together.

## Keymap structure

Although the shield is `corne` (42 positions), the physical board is a 36-key Chocofi — the outer columns are always `&none`. Each layer's `bindings` block is laid out as 3 rows of 12 plus a bottom row of 6 thumb keys.

Layers are named via `#define` at the top of the keymap and referenced by those names (`GALLIUM`, `NUM`, `SYM`, `NAV`, `MISC`, `FUN`, `GAME`, `DEAD`). Layer order in the `keymap` node must match the `#define` numbering.

Custom behaviors defined in the keymap:
- `hml` / `hmr` — home row mods (hold-tap, balanced flavor) with `hold-trigger-key-positions` restricted to the opposite hand.
- `td_shift` — tap dance: one-shot shift on tap, `&caps_word` on double tap.
- `mylt` — custom layer-tap hold-tap; the base layer's thumb keys all use it (e.g. `&mylt NAV SPACE`).

The base layer is the Gallium alphabet layout, not QWERTY. `GAME` and `DEAD` are toggle layers reached via `&tog` from the `MISC` layer (and toggle themselves off).
