# Firmware v3 — FreeRTOS

*Current firmware. See [architecture documentation](../../docs/en/firmware.md) · [dokumentacja architektury](../../docs/pl/firmware.md).*

PlatformIO project for the ESP32-C3, Arduino framework, built on FreeRTOS.

## Quick start

```bash
cp include/secrets.h.example include/secrets.h   # add your Wi-Fi credentials
pio run                                          # build
pio run -t upload                                # flash
pio device monitor                               # serial log
```

`platformio.ini` uploads over the air by default. For the **first flash**, or whenever the board is
not reachable on the network, comment out these two lines and connect over USB:

```ini
upload_protocol = espota
upload_port = 192.168.1.15
```

The same static IP is set in `src/main.cpp` — change both to match your network.

## Layout

```
include/
├── config.h            pins, timings, calibration constants, LCD glyphs
├── rtos_handles.h      queue/task handles, shared data structures, enums
├── secrets.h           Wi-Fi credentials — GIT-IGNORED, create it yourself
├── secrets.h.example   template for the above
└── tasks/              one header per task
src/
├── main.cpp            queue and task creation, Wi-Fi, OTA
└── tasks/
    ├── button_task.cpp   ISR, debounce, auto-repeat, UI state machine
    ├── control_task.cpp  PID loop, PWM output, over-temperature cut-off
    ├── display_task.cpp  all LCD screens, countdown
    ├── fan_task.cpp      fan PWM
    ├── led_task.cpp      chamber LED PWM
    ├── log_task.cpp      serialises log messages onto Serial
    └── sensor_task.cpp   DS18B20, AHT10, BMP280
```

## Dependencies

Resolved automatically by PlatformIO from `platformio.ini`:

| Library | Purpose |
|---|---|
| `paulstoffregen/OneWire` | 1-Wire bus |
| `milesburton/DallasTemperature` | DS18B20 |
| `adafruit/Adafruit BMP280 Library` | BMP280 |
| `adafruit/Adafruit AHTX0` | AHT10 |
| `marcoschwartz/LiquidCrystal_I2C` | LCD 20×4 |
| `br3ttb/PID` | PID controller |
