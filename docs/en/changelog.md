# Changelog

[← back to README](../../README.md) · [🇵🇱 wersja polska](../pl/historia-zmian.md)

---

## v3 — FreeRTOS rewrite *(in progress)*

The largest change in the project. The single Arduino loop was replaced with a proper task-based
design.

**Firmware**

* Complete rewrite onto **FreeRTOS** — each concern isolated in its own task, communicating through
  queues ([details](firmware.md))
* Configuration extracted into a single [`config.h`](../../firmware/v3-freertos/include/config.h) —
  pins, timings, calibration constants and thresholds in one place instead of scattered `#define`s
* **Interrupt-driven buttons** with debouncing and hold-to-repeat handled in the task, not the ISR
* **OTA updates** over Wi-Fi via ArduinoOTA — no more unscrewing the panel to reach the USB port
* **PID gains editable on the device** and persisted to flash (NVS) — tuning survives a reboot
* Wi-Fi connection status icon on the LCD
* Non-blocking networking: no Wi-Fi means no network features, not a dead dryer
* General responsiveness improvements — no more flicker, no more missed button presses

**Mechanics (planned for this version)**

* **Double-walled construction** for better insulation, lower energy use and faster warm-up
* **Filament feed-through holes** so the printer can pull filament directly from the dryer while
  printing
* An additional smaller fan to improve moisture extraction

**Planned**

* MQTT and remote control
* Notifications when drying completes — PC, phone, e-mail

---

## v2 — display and heating improvements

* Display upgraded from **20×2 to 20×4**, with the display code reworked so all values are laid out
  correctly on the larger screen
* Display mount reprinted in **ABS instead of PETG** — it sits near the warm chamber wall, and PETG
  softened there
* New base with a **denser hole pattern**, improving airflow and cutting warm-up time
* **Third heating mat** added, with its own heatsink

<img src="../images/v2/screen_improvement.jpg" alt="Upgraded 20x4 display" width="500">

---

## v1 — first working dryer

The original build: 3D-printed frame with acrylic panels, ESP32-C3 on a prototype board, PID heating
control, an LCD interface and a drying timer, all in a single Arduino sketch.

See the [build log](build-log.md) for photographs of this stage.

The v1 sketch is preserved at
[`firmware/v1-arduino/filament_dryer_v1.ino`](../../firmware/v1-arduino/filament_dryer_v1.ino).
