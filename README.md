# C172 Switch Panel — ESP32 Firmware

Firmware for the **PilotWisdom C172-style switch panel**: a physical cockpit switch board that connects to a PC over USB and works as a standard **24-button HID joystick**. No custom drivers — bind switches in MSFS, X-Plane, Prepar3D, or any sim that accepts generic joystick inputs.

## Features

- **Plug-and-play USB HID** via [TinyUSB](https://github.com/hathach/tinyusb) 
- **11 physical switches** mapped to **24 logical buttons** (active + mirror state per switch)
- **Software debouncing** — 3 consecutive stable samples before a state change is reported
- **Periodic heartbeat** keeps the host aware of panel state even when switches are idle
- **WS2812 status LED** (GPIO 21) blinks on each report for quick visual confirmation

## Hardware

| Item | Detail |
|------|--------|
| MCU | ESP32-S2 or ESP32-S3 (USB-capable) |
| Switch inputs | GPIO 1–11, internal pull-ups, active low |
| Status LED | WS2812 on GPIO 21 |

Each physical switch occupies two HID button slots: one when the switch is **on**, one when it is **off**. That gives simulators separate bind targets for each position without special-case logic.

## Project Structure

```
main/main.c          — GPIO polling, debounce, HID reports, LED
main/serial_auto.h   — device serial number (auto-generated at build)
sdkconfig.defaults   — TinyUSB HID enabled
```

## Building

Requires [ESP-IDF](https://docs.espressif.com/projects/esp-idf/) **v5.0+**.

```bash
idf.py set-target esp32s3   # or esp32s2
idf.py build flash
```

A pre-build step assigns a unique USB serial number (`PWSWP#####`) via `SerialGen/update_serial.ps1`. On Windows builds this runs automatically; ensure the script path in `main/CMakeLists.txt` points to your local SerialGen copy, or set `AUTO_SERIAL` manually in `main/serial_auto.h`.

## License

GPL-3.0 — see [LICENSE](LICENSE).
