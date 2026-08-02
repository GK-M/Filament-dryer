# Hardware

[← back to README](../../README.md) · [🇵🇱 wersja polska](../pl/sprzet.md)

---

## Bill of materials

### Electronics

| Qty | Part | Notes |
|:--:|---|---|
| 1 | ESP32-C3 dev board | `esp32-c3-devkitm-1`, Wi-Fi + USB CDC |
| 3 | MOSFET module | low-side switching of the heating mats |
| 3 | DC-DC step-down converter | generates the 12 V and 5 V rails from 24 V |
| 1 | AHT10 | I²C temperature + relative humidity — **PID process sensor** |
| 1 | BMP280 | I²C temperature + pressure |
| 3 | DS18B20 | 1-Wire, on the heating mats / electronics bay — safety cut-off |
| 1 | LCD 20×4 with I²C backpack | address `0x27` |
| 5 | Push button | menu navigation, momentary, to GND |
| 1 | Rocker switch | mains ON/OFF |
| 1 | Prototype board | carries the ESP32 and the wiring |
| 1 | LED strip | chamber lighting, PWM dimmable |
| 1 | Fan, 120 mm | air circulation, PWM controlled |
| 1 | Power supply, 250 W / 24 V | sized for all three mats at once |
| 1 | XT60 connector | main DC feed |
| — | Wire | sized for 24 V / ~10 A on the heater side |

### Heating

| Qty | Part |
|:--:|---|
| 2 | Silicone heating mat 100 W / 24 V |
| 1 | Silicone heating mat 50 W / 24 V |
| 3 | Heatsink (one per mat) |

### Mechanics

| Qty | Part |
|:--:|---|
| ~1 kg | PETG filament (frame, brackets, mounts) |
| — | ABS filament (display mount, v2 onwards — see [changelog](changelog.md)) |
| — | Acrylic sheet 300×300 mm |

---

## Power rails

```
        ┌── 24 V ──▶ 3× heating mat (via MOSFET modules)
 PSU ───┼── 12 V ──▶ fan + LED strip        (DC-DC step-down)
 250 W  └──  5 V ──▶ ESP32-C3               (DC-DC step-down)
```

The 24 V rail must carry the full 250 W of heating (≈10.4 A) — this is the part of the build where
wire gauge and connector rating actually matter.

---

## Pin map

Defined in [`firmware/v3-freertos/include/config.h`](../../firmware/v3-freertos/include/config.h).

| Function | GPIO | Notes |
|---|:--:|---|
| 1-Wire bus (3× DS18B20) | 3 | shared bus, sensors addressed individually |
| Heater PWM | 2 | LEDC channel 0, 20 kHz, 10-bit |
| Fan PWM | 4 | LEDC channel 2, 20 kHz, 10-bit |
| LED strip PWM | 7 | LEDC channel 1, 20 kHz, 10-bit |
| I²C SDA | 8 | 400 kHz — LCD, AHT10, BMP280 |
| I²C SCL | 9 | |
| Button — Enter | 1 | next screen |
| Button — Mode | 5 | cycles operating mode |
| Button — Screen | 10 | selects which value is being edited |
| Button — Decrease | 20 | with auto-repeat on hold |
| Button — Increase | 21 | with auto-repeat on hold |

All buttons are wired to GND and use the internal pull-ups (`INPUT_PULLUP`), so a press reads LOW.

### DS18B20 addresses

The 1-Wire sensors are addressed explicitly rather than by bus index, so swapping cables cannot
silently reassign which sensor is which. The addresses live in the `adrr_DS` namespace in
`config.h`; if you build your own unit, read your sensors' ROM codes and replace them there.

---

## Sensor roles

| Sensor | Measures | Used for |
|---|---|---|
| AHT10 | chamber air temperature, RH | **PID input** and fan control |
| BMP280 | temperature, pressure | display / logging only |
| DS18B20 ×3 | heating mat and electronics temperature | over-temperature safety cut-off |

The distinction matters: the PID regulates the *air*, not the mats. The mats always run hotter than
the chamber, which is exactly why they need their own independent safety sensors.

---

## Enclosure

The chamber is a 3D-printed frame holding 300×300 mm acrylic panels, rather than a fully printed
box. It uses far less filament, prints in manageable pieces, and lets you see the spools.

### CAD sources — `hardware/v1/cad/`

Autodesk Inventor parts (`.ipt`) and assemblies (`.iam`), with neutral `.stp` exports and sliced
`.3mf` files alongside them.

> **Filenames are intentionally left in Polish.** Inventor assemblies reference their component
> parts by filename — renaming the `.ipt` files would break every `.iam`. The table below is the
> translation key.

| Folder / file | English |
|---|---|
| `Front plexa/` | front acrylic panel |
| `obramówka do plexy/` | acrylic frame trim |
| `tył tył boxa/` | rear of the box |
| `tył za plexą/` | rear section, behind the acrylic |
| `OldVersions/` | superseded revisions |
| `Kątownik …` | angle bracket |
| `Narożnik …` | corner piece |
| `Klin_do_łączenia_34mm` | 34 mm joining wedge |
| `Koniec BOXa` | box end cap |
| `Część 1 / 2 / 3` | part 1 / 2 / 3 |
| `… na kabel` / `… przelotka` | with cable pass-through / grommet |
| `… z dziurą` | with hole |
| `… 3 wcięcia` | with three notches |
| `… x2`, `x3`, `x3szt` | print 2×, 3× |
| `górny` / `dolny` / `środek` | top / bottom / middle |
| `prawy` / `lewy` | right / left |

### Printable STL — `hardware/v2/stl/`

| File | Part | Material |
|---|---|---|
| `base.stl` | chamber base, v2 perforation pattern | ABS |
| `screen_mount.stl` | display mount | **ABS** (PETG deforms near the warm wall) |
| `screen_v2.stl` | display bezel, v2 | ABS |

---

## Assembly notes

* Fit the DS18B20 sensors in **direct contact** with the mats, under the heatsinks — an air gap
  makes the safety cut-off react far too late.
* Keep the 24 V heater wiring physically away from the I²C lines. The mats switch at 20 kHz and will
  happily inject noise into a long, unshielded I²C run.
* The AHT10 should sit in moving air away from the mats, otherwise the PID chases a hot spot instead
  of the chamber temperature.
* Route the mains side so the switch cuts everything upstream of the PSU.
