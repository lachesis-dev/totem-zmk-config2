# Totem ZMK Configuration

Custom ZMK firmware configuration for the [GEIGEIGEIST Totem](https://github.com/GEIGEIGEIST/totem) split keyboard with **Dual battery monitor and ZMK Studio support**.

## Features

- **Colemak-DH Matrix layout** optimized for ortholinear keyboards
- **Dual battery monitoring** - Reports battery levels for both keyboard halves
- **ZMK Studio unlock** for live keymap editing (not in original Totem config)
- **5 layers** optimized for Python and JavaScript development
- **Homerow mods** for comfortable modifier access
- **Mouse support** with scroll and movement controls
- **Combos** for quick access to ESC, dictation, and special characters

## Layers

### BASE - Colemak-DH
Main typing layer with homerow mods (GUI/Alt/Shift/Ctrl).

### CODE
Optimized symbol layer for Python/JavaScript:
- Brackets `[]` `()` on homerow for easy access
- Numbers arranged as numpad on right side
- Frequently-used symbols (`_`, `@`, `#`, `"`, `'`) in comfortable positions

### NAV
Navigation and mouse control layer with arrow keys, page navigation, and mouse movements.

### MOD
System controls including media keys, screen lock macros, and brightness/volume controls.

### ADJ
Function keys, Bluetooth device switching, and ZMK Studio unlock.

## Keymap Visualization

![Totem Keymap](totem-keymap.svg)

## Installation

1. Fork this repository
2. Enable GitHub Actions in your fork
3. Modify `config/totem.keymap` as needed
4. Push changes to trigger automatic firmware build
5. Download firmware from Actions artifacts
6. Flash the halves — see [Flashing & Re-pairing the Split Halves](#flashing--re-pairing-the-split-halves) (**order matters**)

## Flashing & Re-pairing the Split Halves

> [!IMPORTANT]
> **Reset BOTH halves *before* flashing EITHER half.**
> If you reset-and-flash one half at a time (reset left → flash left → reset right →
> flash right), the left half bonds to the right half's *old* identity and then can't
> re-bond once the right is reset. The halves then silently fail to connect, and only
> the left half types.

All `.uf2` files must come from the **same** GitHub Actions run.

### Re-pairing the halves (first-time setup, or after any firmware/BLE change)

Do these in this exact order:

1. **Reset BOTH halves first** — put each into bootloader (double-tap reset) and copy:
   - `settings_reset-xiao_ble_nrf52840_zmk.uf2` → **left**
   - `settings_reset-xiao_ble_nrf52840_zmk.uf2` → **right**
2. **Then flash the keyboard firmware** (matching halves):
   - `totem_left-xiao_ble_nrf52840_zmk.uf2` → **left**
   - `totem_right-xiao_ble_nrf52840_zmk.uf2` → **right**
3. **Power both halves on at the same time** so the left (central) discovers and bonds
   to the right (peripheral) cleanly.

### Routine keymap update (no re-pairing needed)

If you only changed the keymap, skip the reset and just flash both halves with the new
`totem_left` / `totem_right` files.

> [!NOTE]
> The **left half is the central** — it talks to the computer and types over USB/BLE.
> The **right half is the peripheral** — it relays its keys to the left over BLE and
> **never types over its own USB connection**. Test the right half by pressing its keys
> while the left half is connected to the computer.

## Hardware

- **Keyboard:** GEIGEIGEIST Totem (38-key split)
- **Controller:** Seeeduino XIAO BLE (nRF52840)
- **Firmware:** ZMK with Studio support

## Special Features

### Combos
- **Q + W:** ESC
- **N + M:** Dictation (Alt+Space)
- **U + Y:** ñ character

### Macros
- **Mac Lock:** Cmd+Ctrl+Q
- **Win Lock:** Win+L

### Battery Monitoring
Configured for split battery level reporting to support peripheral battery monitoring apps.

## Changing the Keyboard Name

To change the Bluetooth device name:

1. Edit `config/totem.conf` and set:
   ```
   CONFIG_ZMK_KEYBOARD_NAME="Your Custom Name"
   ```

2. Build the firmware (GitHub Actions will create 3 files including `settings_reset`)

3. Flash the firmware — **reset both halves before flashing either**
   (see [Flashing & Re-pairing the Split Halves](#flashing--re-pairing-the-split-halves)):
   - `settings_reset-xiao_ble_nrf52840_zmk.uf2` → **both** halves first
   - `totem_left-xiao_ble_nrf52840_zmk.uf2` → left half
   - `totem_right-xiao_ble_nrf52840_zmk.uf2` → right half

4. Clear the host Bluetooth bond with `&bt BT_CLR_ALL`

Note: The settings reset is required because the keyboard name is stored in persistent memory.
