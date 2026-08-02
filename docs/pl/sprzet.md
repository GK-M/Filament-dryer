# Sprzęt

[← powrót do README](../../README.pl.md) · [🇬🇧 English version](../en/hardware.md)

---

## Lista części

### Elektronika

| Ilość | Element | Uwagi |
|:--:|---|---|
| 1 | Płytka ESP32-C3 | `esp32-c3-devkitm-1`, Wi-Fi + USB CDC |
| 3 | Moduł MOSFET | przełączanie mat grzewczych po stronie masy |
| 3 | Przetwornica step-down | wytwarza szyny 12 V i 5 V z 24 V |
| 1 | AHT10 | I²C, temperatura + wilgotność względna — **czujnik procesowy PID** |
| 1 | BMP280 | I²C, temperatura + ciśnienie |
| 3 | DS18B20 | 1-Wire, na matach grzewczych / przy elektronice — zabezpieczenie |
| 1 | LCD 20×4 z konwerterem I²C | adres `0x27` |
| 5 | Przycisk | nawigacja po menu, monostabilny, do masy |
| 1 | Włącznik kołyskowy | zasilanie ON/OFF |
| 1 | Płytka prototypowa | nośnik ESP32 i okablowania |
| 1 | Taśma LED | oświetlenie komory, ściemniana PWM |
| 1 | Wentylator 120 mm | obieg powietrza, sterowany PWM |
| 1 | Zasilacz 250 W / 24 V | dobrany pod wszystkie trzy maty naraz |
| 1 | Złącze XT60 | główne zasilanie DC |
| — | Przewody | dobrane pod 24 V / ok. 10 A po stronie grzania |

### Grzanie

| Ilość | Element |
|:--:|---|
| 2 | Mata grzewcza silikonowa 100 W / 24 V |
| 1 | Mata grzewcza silikonowa 50 W / 24 V |
| 3 | Radiator (po jednym na matę) |

### Mechanika

| Ilość | Element |
|:--:|---|
| ok. 1 kg | Filament PETG (rama, kątowniki, mocowania) |
| — | Filament ABS (mocowanie ekranu, od v2 — patrz [historia zmian](historia-zmian.md)) |
| — | Pleksi 300×300 mm |

---

## Zasilanie

```
        ┌── 24 V ──▶ 3× mata grzewcza (przez moduły MOSFET)
Zasilacz┼── 12 V ──▶ wentylator + taśma LED   (przetwornica step-down)
 250 W  └──  5 V ──▶ ESP32-C3                 (przetwornica step-down)
```

Szyna 24 V musi przenieść pełne 250 W grzania (ok. 10,4 A) — to ten fragment budowy, w którym
przekrój przewodu i obciążalność złączy naprawdę mają znaczenie.

---

## Mapa pinów

Zdefiniowana w [`firmware/v3-freertos/include/config.h`](../../firmware/v3-freertos/include/config.h).

| Funkcja | GPIO | Uwagi |
|---|:--:|---|
| Magistrala 1-Wire (3× DS18B20) | 3 | wspólna magistrala, czujniki adresowane pojedynczo |
| PWM grzałki | 2 | LEDC kanał 0, 20 kHz, 10 bitów |
| PWM wentylatora | 4 | LEDC kanał 2, 20 kHz, 10 bitów |
| PWM taśmy LED | 7 | LEDC kanał 1, 20 kHz, 10 bitów |
| I²C SDA | 8 | 400 kHz — LCD, AHT10, BMP280 |
| I²C SCL | 9 | |
| Przycisk — Enter | 1 | następny ekran |
| Przycisk — Tryb | 5 | przełącza tryb pracy |
| Przycisk — Ekran | 10 | wybiera edytowaną wartość |
| Przycisk — Zmniejsz | 20 | z auto-powtarzaniem przy przytrzymaniu |
| Przycisk — Zwiększ | 21 | z auto-powtarzaniem przy przytrzymaniu |

Wszystkie przyciski są podpięte do masy i korzystają z wewnętrznych podciągnięć (`INPUT_PULLUP`),
więc wciśnięcie daje stan niski.

### Adresy DS18B20

Czujniki 1-Wire są adresowane jawnie, a nie po indeksie na magistrali — dzięki temu przepięcie
przewodu nie przypisze po cichu odczytu do innego czujnika. Adresy znajdują się w przestrzeni nazw
`adrr_DS` w `config.h`; przy budowie własnego egzemplarza trzeba odczytać kody ROM swoich czujników
i podmienić je w tym miejscu.

---

## Rola czujników

| Czujnik | Mierzy | Do czego służy |
|---|---|---|
| AHT10 | temperatura powietrza w komorze, wilgotność | **wejście PID** i sterowanie wentylatorem |
| BMP280 | temperatura, ciśnienie | tylko wyświetlanie / logowanie |
| DS18B20 ×3 | temperatura mat i elektroniki | zabezpieczenie przed przegrzaniem |

To rozróżnienie jest istotne: PID reguluje *powietrze*, a nie maty. Maty zawsze są cieplejsze od
komory — i właśnie dlatego potrzebują własnych, niezależnych czujników zabezpieczających.

---

## Obudowa

Komora to drukowana rama spinająca panele z pleksi 300×300 mm, a nie w całości drukowane pudełko.
Zużywa znacznie mniej filamentu, drukuje się w wygodnych kawałkach i pozwala widzieć szpule.

### Pliki CAD — `hardware/v1/cad/`

Części (`.ipt`) i złożenia (`.iam`) z Autodesk Inventor, obok nich neutralne eksporty `.stp`
i pocięte pliki `.3mf`.

> **Nazwy plików celowo pozostały po polsku.** Złożenia Inventora odwołują się do części po nazwie
> pliku — zmiana nazw `.ipt` rozsypałaby każdy `.iam`. W wersji angielskiej dokumentacji znajduje
> się tabela tłumaczeń nazw.

### Pliki STL do druku — `hardware/v2/stl/`

| Plik | Element | Materiał |
|---|---|---|
| `base.stl` | podstawa komory, układ otworów z v2 | PETG |
| `screen_mount.stl` | mocowanie wyświetlacza | **ABS** (PETG odkształca się przy ciepłej ściance) |
| `screen_v2.stl` | ramka wyświetlacza, v2 | ABS |

---

## Uwagi montażowe

* Czujniki DS18B20 zamontuj w **bezpośrednim kontakcie** z matami, pod radiatorami — szczelina
  powietrzna sprawia, że zabezpieczenie reaguje o wiele za późno.
* Prowadź okablowanie 24 V grzałek fizycznie z dala od linii I²C. Maty przełączają się z
  częstotliwością 20 kHz i chętnie wprowadzą zakłócenia w długi, nieekranowany odcinek I²C.
* AHT10 powinien znajdować się w strumieniu powietrza, z dala od mat — inaczej PID goni za lokalnym
  gorącym punktem zamiast za temperaturą komory.
* Poprowadź stronę sieciową tak, żeby włącznik odcinał wszystko przed zasilaczem.
