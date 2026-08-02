# Roadmap

[← back to README](../../README.md) · [🇵🇱 wersja polska](../pl/plany.md)

---

## Done

- [x] Drying timer
- [x] Over-temperature protection — setpoint forced to zero when a mat exceeds the limit
- [x] Dedicated display task
- [x] Firmware updates over Wi-Fi (ArduinoOTA)
- [x] PID gains editable on the device and persisted to flash
- [x] Wi-Fi status icon on the LCD

## Next

- [ ] **Fan control from humidity** — replace the current threshold with a proper control law
- [ ] **LED control** — hold *Enter* to toggle the backlight and the chamber LED together
- [ ] **Cooldown countdown** — when `pid_data.error` is set, show how long until the mats are back
      under the safe threshold instead of a static fault screen
- [ ] **Fault screen** — finish the `TEMP_ERROR` layout
- [ ] **Operating modes** — presets per filament type (PLA / PETG / ABS / Nylon) behind the
      `Mode` button, which currently only cycles an unused enum

## Later

- [ ] **MQTT or raw TCP/IP** — report state to a server
- [ ] **Remote control** — set temperature and time from outside the device
- [ ] **Notifications on completion** — PC, phone, e-mail
- [ ] **Double-walled chamber** — better insulation, faster warm-up, lower energy use
- [ ] **Filament feed-through** — let the printer draw filament straight from the dryer
- [ ] **Second, smaller fan** — improve moisture extraction

---

## Known issues

Things that are currently wrong or unfinished, listed rather than hidden.

| # | Where | Issue |
|:--:|---|---|
| 1 | `display_task.cpp`, `Screen::TEMP_ERROR` | Malformed format strings (`"%5.1%d"` with a `float` argument). The fault screen will print garbage — this screen is unfinished. |
| 2 | `sensor_task.cpp` | In the AHT10 log line the arguments are swapped: temperature is printed where humidity is labelled and vice versa. Cosmetic, but misleading when reading logs. |
| 3 | `config.h`, `adrr_DS::addr2` | A stray trailing `\` line-continues the following comment, so the `//Temp podzespołów` note is swallowed. Harmless, but it should not be there. |
| 4 | `rtos_handles.h`, `enum class Screen` | `COUNT` sits in the middle of the enum, so `Enter` cycles only the first four screens. It works, but it is fragile — adding a screen in the wrong place silently changes the navigation. |
| 5 | `fan_task.cpp` | Fan control is a bang-bang threshold at 20 % RH (duty 1023 above, 800 below), not the humidity-driven regulation the UI advertises. `Screen::PID_fan` is an empty placeholder. |
| 6 | `led_task.cpp` | The task sets the LED to full brightness and then loops doing nothing. Long-press LED/backlight control is not implemented. |
| 7 | `button_task.cpp` | The drying timer starts implicitly on the first time increment, rather than on an explicit start action. Setting the time and then walking away starts heating unintentionally. |
| 8 | `control_task.cpp` | `pid.SetTunings()` is called every 10 ms. Harmless with this library, but it is doing work on every iteration for a value that changes only when a button is pressed. |
| 9 | project-wide | No automated tests. `test/` is an empty PlatformIO placeholder. |

Contributions and bug reports are welcome — open an issue.
