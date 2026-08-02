# Firmware v1 — Arduino sketch

*Historical. Kept for reference; superseded by [v3](../v3-freertos/).*

The original firmware: a single 729-line `.ino` file with one `loop()` handling sensor reads, the PID
loop, LCD redraws and button polling in sequence.

It worked and dried filament, but it showed the limits of the approach:

* the LCD flickered on every full redraw
* button presses were dropped while the display was being written
* every new feature had to be woven into the same growing loop
* configuration was spread across dozens of `#define`s and globals

Those problems are what motivated the [FreeRTOS rewrite](../v3-freertos/). Reading the two side by
side is the clearest illustration of what the rewrite bought.

## Building

Open `filament_dryer_v1.ino` in the Arduino IDE, select an ESP32-C3 board, and install:

`LiquidCrystal_I2C` · `Adafruit AHTX0` · `Adafruit BMP280` · `OneWire` · `DallasTemperature` ·
`PID by Brett Beauregard`
