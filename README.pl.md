# Suszarka do filamentu

[🇬🇧 English](README.md) · **🇵🇱 Polski**

Komora susząca do filamentu o mocy 250 W, zbudowana od zera: mechanika zaprojektowana w Autodesk
Inventor i wydrukowana na drukarce 3D, elektronika zmontowana na płytce prototypowej, firmware
napisany na ESP32-C3 — najpierw jako pojedynczy szkic Arduino, następnie przepisany na **FreeRTOS**
z podziałem na taski, kolejkami i aktualizacją bezprzewodową.

<p align="center">
  <img src="docs/images/hero.jpg" alt="Złożona suszarka z dwiema szpulami w środku" width="620">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/MCU-ESP32--C3-informational">
  <img src="https://img.shields.io/badge/framework-Arduino%20%2F%20PlatformIO-blue">
  <img src="https://img.shields.io/badge/RTOS-FreeRTOS-green">
  <img src="https://img.shields.io/badge/regulacja-PID%20%2B%20PWM-orange">
  <img src="https://img.shields.io/badge/status-v3%20w%20trakcie-yellow">
</p>

---

## Spis treści

| Dokument | Co zawiera |
|---|---|
| **[📸 Dziennik budowy](docs/pl/dziennik-budowy.md)** | Zdjęcia z całego procesu budowy, etap po etapie |
| [🔧 Sprzęt](docs/pl/sprzet.md) | Lista części, mapa pinów, zasilanie, pliki CAD i STL |
| [💻 Firmware](docs/pl/firmware.md) | Architektura tasków i kolejek FreeRTOS, pętla PID, interfejs, OTA |
| [📝 Historia zmian](docs/pl/historia-zmian.md) | Co zmieniło się między v1, v2 i v3 |
| [🗺️ Plany](docs/pl/plany.md) | Planowane funkcje i znane problemy |

---

## Co robi

Wilgotny filament drukuje się źle — nitkowanie, strzelanie, słaba przyczepność warstw. To pudełko
nagrzewa szczelną komorę do zadanej temperatury, utrzymuje ją regulatorem PID i pracuje przez czas
ustawiony na panelu przednim.

* **Ustawiasz temperaturę** (0–100 °C co 5 °C; ok. 70 °C to praktyczne maksimum obudowy)
* **Ustawiasz czas suszenia** (15 min na kliknięcie, do 24 h) — po jego upływie grzanie się wyłącza
* **Widzisz wszystko na bieżąco** na wyświetlaczu 20×4: temperaturę komory, wilgotność, temperatury
  mat, wypełnienie PWM, pozostały czas i status Wi-Fi
* **Nastawy PID edytujesz z urządzenia** — Kp/Ki/Kd zmieniasz przyciskami, a wartości zapisują się
  w pamięci flash i przeżywają restart
* **Wgrywasz firmware bez kabla** — ArduinoOTA przez Wi-Fi

## Jak to działa

```
                 ┌──────────── ESP32-C3 ────────────┐
  AHT10  ──I²C──▶│  temp. komory ──▶ PID ──▶ PWM ───┼──▶ MOSFET ──▶ 3× mata grzewcza (250 W)
  BMP280 ──I²C──▶│  temperatura / ciśnienie         │
                 │                                  │
 3× DS18B20 ─1W─▶│  temp. mat ──▶ zabezpieczenie ───┼──▶ wymuszenie setpointu = 0
                 │                                  │
 5× przycisk ─IRQ▶│ automat stanów interfejsu ──────┼──▶ LCD 20×4 (I²C)
                 │                                  ├──▶ PWM wentylatora (wg wilgotności)
        Wi-Fi ──▶│  ArduinoOTA                      └──▶ PWM taśmy LED
                 └──────────────────────────────────┘
```

Regulator PID działa na **temperaturze powietrza w komorze** (AHT10). Czujniki DS18B20 są przyklejone
do mat grzewczych i służą wyłącznie jako zabezpieczenie: gdy którykolwiek przekroczy **110 °C**,
setpoint i wyjście PWM są zerowane, a grzanie wraca dopiero wtedy, gdy wszystkie maty spadną poniżej
**70 °C**.

Szczegóły: **[architektura firmware'u](docs/pl/firmware.md)** · **[sprzęt](docs/pl/sprzet.md)**

## Najważniejsze parametry

| | |
|---|---|
| Sterownik | ESP32-C3 (`esp32-c3-devkitm-1`), framework Arduino, FreeRTOS |
| Grzanie | 3× mata silikonowa 24 V z radiatorami — 2×100 W + 1×50 W = **250 W** |
| Maks. temperatura komory | ok. 70 °C |
| Czujnik procesowy | AHT10 (temperatura + wilgotność względna) |
| Czujniki pomocnicze | BMP280 (temperatura, ciśnienie), 3× DS18B20 (temperatura mat / elektroniki) |
| Sterowanie mocą | moduły MOSFET, PWM (LEDC) 20 kHz, rozdzielczość 10 bitów |
| Napięcia zasilania | 24 V (maty) · 12 V (wentylator, LED) · 5 V (ESP32), przetwornice step-down |
| Interfejs | LCD 20×4 I²C pod adresem `0x27`, 5 przycisków, oświetlenie LED komory |
| Łączność | Wi-Fi, aktualizacje firmware'u przez ArduinoOTA |
| Obudowa | wydrukowana rama PETG + panele z pleksi 300×300 mm |

## Struktura repozytorium

```
filament-dryer/
├── docs/
│   ├── en/            dokumentacja angielska
│   ├── pl/            dokumentacja polska
│   ├── images/        zdjęcia z budowy
│   └── media/         film z budowy
├── firmware/
│   ├── v1-arduino/    pierwotny szkic Arduino w jednym pliku (zachowany dla porównania)
│   └── v3-freertos/   aktualny projekt PlatformIO — FreeRTOS, OTA, NVS
└── hardware/
    ├── v1/cad/        części i złożenia Inventor, STEP, 3MF
    └── v2/stl/        pliki STL do druku (podstawa, mocowanie ekranu)
```

## Kompilacja firmware'u

Aktualny firmware to projekt [PlatformIO](https://platformio.org/).

```bash
git clone https://github.com/GK-M/Filament_dryer.git
cd Filament_dryer/firmware/v3-freertos

# dane Wi-Fi nie trafiają do repozytorium — utwórz plik z szablonu
cp include/secrets.h.example include/secrets.h
$EDITOR include/secrets.h

pio run                 # kompilacja
pio run -t upload       # wgranie (domyślnie przez OTA — patrz niżej)
pio device monitor      # podgląd logów
```

Plik `platformio.ini` jest skonfigurowany do **wgrywania przez OTA** na stały adres:

```ini
upload_protocol = espota
upload_port = 192.168.1.15
```

Przy pierwszym wgraniu — albo gdy płytki nie ma w sieci — zakomentuj te dwie linie i wgraj przez USB.
Stały adres IP ustawiany jest również w `src/main.cpp`; zmień oba miejsca pod swoją sieć.

## Status

v1 i v2 są skończone i działają. **v3 jest w trakcie rozwoju** — firmware działa, ale kilka ekranów
i regulacja wentylatora nie są jeszcze dokończone. Wszystko, co jest niedokończone lub błędne, jest
otwarcie wypisane w [planach](docs/pl/plany.md#znane-problemy).

## Licencja

Projekt udostępniony na [licencji MIT](LICENSE).

> ⚠️ **Uwaga o bezpieczeństwie.** Urządzenie pobiera 250 W mocy grzewczej i pracuje bez nadzoru przez
> wiele godzin. Programowe zabezpieczenie termiczne to zabezpieczenie programowe, a nie zamiennik
> bezpiecznika termicznego, zasilacza o odpowiedniej mocy i prawidłowo dobranego przekroju
> przewodów. Budujesz i używasz na własną odpowiedzialność.
