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

