# lily58-wireless-zmk-config

Personal [ZMK](https://zmk.dev) firmware configuration for a wireless Lily58 split keyboard (nice!nano v2 controllers on each half). See `CLAUDE.md` for a full breakdown of the repo layout.

## Flashing the firmware

Firmware is built automatically by GitHub Actions on every push — there's no local build step. Follow these steps to get it onto your keyboard.

### 1. Download the built firmware

1. Push your changes (or just wait for the latest push to finish building).
2. Open this repo's **Actions** tab on GitHub and select the most recent successful workflow run.
3. Download the `firmware` artifact (a `.zip`).
4. Extract it — you should get three `.uf2` files, one per entry in `build.yaml`:
   - `lily58_left-nice_nano_v2-zmk.uf2`
   - `lily58_right-nice_nano_v2-zmk.uf2`
   - `settings_reset-nice_nano_v2-zmk.uf2`

### 2. Flash each half

For each half of the keyboard:

1. Plug that half into your computer via USB-C.
2. **Double-tap the reset button** on the nice!nano to enter bootloader mode. A USB drive (usually named `NICENANO`) should appear.
3. Copy the matching `.uf2` file onto that drive — `lily58_left-*.uf2` for the left half, `lily58_right-*.uf2` for the right half. **Don't mix them up**, each half only accepts its own build.
4. The drive will automatically unmount and the controller will reboot with the new firmware. If the `NICENANO` drive reappears immediately after copying, the flash failed — try again.
5. Repeat for the other half.

Test each half over USB first (it should show up as a keyboard) before relying on the wireless connection between halves.

### 3. Reconnect the halves

After flashing both sides, reset them at the same time (double-tap reset on both, or unplug/replug both). Within a few seconds they should automatically reconnect to each other over BLE, and the OLEDs should come up.

## Resetting Bluetooth pairings

If the halves won't pair, or a host device (laptop/phone) won't connect properly, use the `settings_reset` build to wipe stored Bluetooth settings:

1. Flash `settings_reset-nice_nano_v2-zmk.uf2` to **both** halves, following the same double-tap-reset + copy process above.
   - This firmware has Bluetooth disabled on purpose, so the halves won't auto-pair while you're resetting them — this is expected.
   - It clears **all** persisted settings on that half (BT profiles, output selection, RGB/underglow state, etc.), not just pairing.
2. Once both halves have been flashed with the reset firmware, **re-flash your normal firmware** (`lily58_left-*.uf2` / `lily58_right-*.uf2`) to both halves again, following the steps in [Flashing the firmware](#flashing-the-firmware).
3. Reset both halves together as in [Reconnect the halves](#3-reconnect-the-halves) and re-pair with your host device(s) from a clean state.

## Reference

- [ZMK docs: Installing ZMK / flashing firmware](https://zmk.dev/docs/user-setup)
- [ZMK docs: Connection troubleshooting](https://zmk.dev/docs/troubleshooting/connection-issues)
- [ZMK docs: Persistent settings](https://zmk.dev/docs/config/settings)
