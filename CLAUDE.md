# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a personal [ZMK](https://zmk.dev) firmware configuration repo for a Lily58 wireless split keyboard (nice!nano v2 controllers). It's config-only — there is no application source code and no local build/test tooling. Firmware is compiled remotely by GitHub Actions, which delegates to ZMK's own reusable build workflow.

## Building firmware

- There are no local build or lint commands. Firmware builds happen on GitHub Actions (`.github/workflows/build.yml`, triggered on push/PR/dispatch), which calls ZMK's reusable `build-user-config.yml` workflow pinned to `v0.3`.
- Board + shield combinations to build are defined in `build.yaml` (format documented in the file's header comments). Currently builds `nice_nano_v2` against `lily58_left`, `lily58_right`, and `settings_reset`.
- ZMK firmware version is pinned via the `revision:` field in `config/west.yml` (currently `v0.3`). If bumping it, also update the `@v0.3` ref used in `.github/workflows/build.yml` so both stay in sync.
- To validate changes, push and watch the Actions build succeed — there's no local way to compile.

## Architecture

- `config/lily58.keymap` is the single source of truth for key bindings, written in ZMK's devicetree syntax (`&kp`, `&mo`, `&bt`, `&trans`, `&ext_power`, `&inc_dec_kp` for the rotary encoder). Each layer's `bindings` block is preceded by an ASCII-art comment mapping physical keys to bindings — keep that comment in sync when editing bindings.
- Three layers exist: `default_layer` (base typing layer), `lower_layer` (Bluetooth profile selection via `&bt`, F-keys, shifted symbols, `ext_power` controls), `raise_layer` (arrow keys, numpad-style symbols).
- `config/lily58.conf` holds Kconfig feature flags and tuning — OLED display, BLE TX power/stability settings, sleep/idle timeouts. Each option is commented inline with its purpose.
- The `settings_reset` shield entry in `build.yaml` builds a utility firmware image for wiping saved BT pairings/settings from the controller — it does not use the keymap.
- `boards/shields/` is intentionally empty (just `.gitkeep`); the `lily58_left`/`lily58_right` shield definitions are not vendored here and instead resolve from the upstream ZMK module pulled in via `config/west.yml`.
- `zephyr/module.yml` declares this repo as a Zephyr module with `board_root: .`, which is what lets ZMK's build tooling find `boards/` and `config/` here.
