# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a modular **ESPHome + LVGL** configuration repository for building smart home touch displays that integrate with Home Assistant. It is not a compiled codebase — all files are YAML configurations that ESPHome compiles and flashes to ESP32 devices.

## Workflow

**No build scripts or test infrastructure exist.** Changes are validated by:
1. Compiling via the ESPHome dashboard or CLI (`esphome compile <device>/esphome.yaml`)
2. Flashing to a physical device (USB first time, OTA thereafter)
3. Verifying behavior in Home Assistant

To run ESPHome locally:
```bash
esphome dashboard            # Web UI for managing devices
esphome compile <device>/esphome.yaml   # Compile without flashing
esphome run <device>/esphome.yaml       # Compile and flash via USB
```

Secrets (WiFi credentials, HA API keys) are stored outside the repo in ESPHome's `secrets.yaml` and referenced with `!secret` tags — never hardcode them.

## Repository Architecture

### Device Structure

Each device directory follows this layout:

```
<device-name>/
├── esphome.yaml        # Entry point: WiFi, board type, includes package.yaml
├── package.yaml        # Aggregates all sub-packages via !include
├── device/
│   ├── device.yaml     # Hardware init: ESP32 variant, display driver, touch, backlight GPIO
│   ├── sensors.yaml    # HA integration: binary sensors, text sensors, globals, scripts
│   └── lvgl.yaml       # Full UI: pages, buttons, labels, event handlers
├── addon/
│   ├── time.yaml       # NTP sync and clock update scripts
│   ├── backlight.yaml  # Screensaver logic and backlight PWM
│   └── network.yaml    # WiFi diagnostics exposed to HA
├── assets/
│   ├── fonts.yaml      # Embedded fonts (Roboto + Material Design Icons)
│   └── icons.yaml      # MDI codepoint mappings
└── theme/
    └── button.yaml     # Substitution variables for colors, sizes, padding, radius
```

### Active Devices

| Directory | Hardware | Display |
|-----------|----------|---------|
| `guition-esp32-p4-jc1060p470/` | ESP32-P4 (360MHz, 16MB) | 7" MIPI DSI |
| `guition-esp32-s3-4848s040/` | ESP32-S3 | 4" |
| `guition-esp32-p4-jc4880p443/` | ESP32-P4 | 8.8" MIPI DSI |
| `guition-esp32-p4-jc8012p4a1/` | ESP32-P4 | 12.1" MIPI DSI |

`archived/` contains legacy device configs no longer in use.

### Key Design Patterns

**Substitutions for theming** — `theme/button.yaml` defines all colors and dimensions as substitution variables (e.g., `button_on_color`, `button_control_color`, `button_height`). UI components reference these via `${variable_name}`. Change appearance by editing these values, not individual widgets.

**LVGL UI conventions:**
- Buttons use `checkable: true` to represent on/off state
- `on_long_press` opens a detail/control panel page
- Page navigation is handled via `lvgl.page.show` actions
- Scripts in `sensors.yaml` encapsulate multi-step HA service calls

**Home Assistant sensor pattern** — `sensors.yaml` defines `homeassistant` platform sensors that mirror HA entity states/attributes as ESPHome globals. LVGL event handlers read these globals to determine what to display or which HA service to call.

**External components** — The JC1060P470 device uses p1ngb4ck's ESPHome fork for advanced display/audio features not in mainline ESPHome. Check `device.yaml` for `external_components` entries when adding features to that device.
