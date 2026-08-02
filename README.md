# Filament Dryer

**🇬🇧 English** · [🇵🇱 Polski](README.pl.md)

A 250 W heated drying chamber for 3D-printing filament, built from scratch: mechanics designed in
Autodesk Inventor and 3D-printed, electronics wired on a prototype board, firmware written for an
ESP32-C3 — first as a single Arduino sketch, then rewritten on **FreeRTOS** with tasks, queues and
over-the-air updates.

<p align="center">
  <img src="docs/images/hero.jpg" alt="Assembled filament dryer with two spools inside" width="620">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/MCU-ESP32--C3-informational">
  <img src="https://img.shields.io/badge/framework-Arduino%20%2F%20PlatformIO-blue">
  <img src="https://img.shields.io/badge/RTOS-FreeRTOS-green">
  <img src="https://img.shields.io/badge/control-PID%20%2B%20PWM-orange">
  <img src="https://img.shields.io/badge/status-v3%20in%20progress-yellow">
</p>

---

## Table of contents

| Document | What is inside |
|---|---|
| **[📸 Build log](docs/en/build-log.md)** | Photos from the whole build, stage by stage |
| [🔧 Hardware](docs/en/hardware.md) | Bill of materials, pin map, power rails, CAD & STL files |
| [💻 Firmware](docs/en/firmware.md) | FreeRTOS task/queue architecture, PID loop, UI, OTA |
| [📝 Changelog](docs/en/changelog.md) | What changed between v1, v2 and v3 |
| [🗺️ Roadmap](docs/en/roadmap.md) | Planned features and known issues |

---

## What it does

Wet filament prints badly — stringing, popping, weak layer adhesion. This box heats a sealed chamber
to a set temperature, holds it with a PID loop, and runs for a time you dial in on the front panel.

* **Set a temperature** (0–100 °C in 5 °C steps; ~70 °C is the practical ceiling of the enclosure)
* **Set a drying time** (15 min per click, up to 24 h) — the heater switches off when it elapses
* **Watch everything live** on a 20×4 LCD: chamber temperature, humidity, mat temperatures,
  PWM duty, remaining time, Wi-Fi status
* **Tune the PID on the device** — Kp/Ki/Kd are edited with the buttons and stored in flash,
  so they survive a reboot
* **Flash new firmware without a cable** — ArduinoOTA over Wi-Fi

## How it works

```
                 ┌──────────── ESP32-C3 ────────────┐
  AHT10  ──I²C──▶│  chamber temp ──▶ PID ──▶ PWM ───┼──▶ MOSFET ──▶ 3× heating mat (250 W)
  BMP280 ──I²C──▶│  temp / pressure                 │
                 │                                  │
 3× DS18B20 ─1W─▶│  mat temp ──▶ over-temp cut-off ─┼──▶ setpoint forced to 0
                 │                                  │
  5× button ─IRQ▶│  UI state machine ───────────────┼──▶ LCD 20×4 (I²C)
                 │                                  ├──▶ fan PWM (humidity driven)
        Wi-Fi ──▶│  ArduinoOTA                      └──▶ LED strip PWM
                 └──────────────────────────────────┘
```

The PID acts on the **chamber air temperature** (AHT10). The DS18B20 sensors sit on the heating mats
and are used purely for safety: if any of them passes **110 °C** the setpoint and PWM output are
forced to zero, and heating is only re-armed once all mats drop below **70 °C**.

Full details: **[Firmware architecture](docs/en/firmware.md)** · **[Hardware](docs/en/hardware.md)**

## Key specifications

| | |
|---|---|
| Controller | ESP32-C3 (`esp32-c3-devkitm-1`), Arduino framework, FreeRTOS |
| Heating | 3× silicone mat 24 V with heatsinks — 2×100 W + 1×50 W = **250 W** |
| Max chamber temperature | ~70 °C |
| Process sensor | AHT10 (temperature + relative humidity) |
| Auxiliary sensors | BMP280 (temperature, pressure), 3× DS18B20 (mat / electronics temperature) |
| Actuator drive | MOSFET modules, LEDC PWM 20 kHz, 10-bit resolution |
| Power rails | 24 V (mats) · 12 V (fan, LED) · 5 V (ESP32), via DC-DC step-down converters |
| Interface | LCD 20×4 I²C @ `0x27`, 5 buttons, LED chamber lighting |
| Connectivity | Wi-Fi, ArduinoOTA firmware updates |
| Enclosure | 3D-printed PETG frame + 300×300 mm acrylic panels |

## Repository layout

```
filament-dryer/
├── docs/
│   ├── en/            English documentation
│   ├── pl/            Polish documentation
│   ├── images/        Build photos
│   └── media/         Build video
├── firmware/
│   ├── v1-arduino/    Original single-file Arduino sketch (kept for reference)
│   └── v3-freertos/   Current PlatformIO project — FreeRTOS, OTA, NVS
└── hardware/
    ├── v1/cad/        Inventor parts & assemblies, STEP, 3MF
    └── v2/stl/        Printable STL files (base, screen mount)
```

## Building the firmware

The current firmware is a [PlatformIO](https://platformio.org/) project.

```bash
git clone https://github.com/GK-M/Filament_dryer.git
cd Filament_dryer/firmware/v3-freertos

# Wi-Fi credentials are not in the repository — create them from the template
cp include/secrets.h.example include/secrets.h
$EDITOR include/secrets.h

pio run                 # build
pio run -t upload       # flash (OTA by default — see below)
pio device monitor      # serial log
```

`platformio.ini` is configured for **OTA upload** to a fixed address:

```ini
upload_protocol = espota
upload_port = 192.168.1.15
```

For the first flash — or any time the board is not on the network — comment those two lines out and
upload over USB. The static IP is also set in `src/main.cpp`; change both to match your network.

## Status

v1 and v2 are complete and working. **v3 is under active development** — the firmware runs, but a
few screens and the fan control loop are still unfinished. Everything known to be incomplete or
buggy is listed openly in the [roadmap](docs/en/roadmap.md#known-issues).

## Licence

Released under the [MIT Licence](LICENSE).

> ⚠️ **Safety note.** This device draws 250 W of heating power and runs unattended for hours.
> The firmware over-temperature cut-off is a software safeguard, not a substitute for a proper
> thermal fuse, an adequately rated power supply and correctly sized wiring. Build and use at your
> own risk.
