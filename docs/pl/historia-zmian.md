# Historia zmian

[← powrót do README](../../README.pl.md) · [🇬🇧 English version](../en/changelog.md)

---

## v3 — przepisanie na FreeRTOS *(w trakcie)*

Największa zmiana w projekcie. Pojedyncza pętla Arduino została zastąpiona porządnym podziałem na
taski.

**Firmware**

* Całkowite przepisanie na **FreeRTOS** — każda odpowiedzialność w osobnym tasku, komunikacja przez
  kolejki ([szczegóły](firmware.md))
* Konfiguracja wyciągnięta do jednego pliku
  [`config.h`](../../firmware/v3-freertos/include/config.h) — piny, czasy, stałe kalibracyjne i progi
  w jednym miejscu zamiast rozsypanych `#define`
* **Przyciski na przerwaniach**, z debounce'em i auto-powtarzaniem obsługiwanymi w tasku, a nie w ISR
* **Aktualizacje OTA** przez Wi-Fi (ArduinoOTA) — koniec z rozkręcaniem panelu, żeby dostać się do
  portu USB
* **Nastawy PID edytowalne z urządzenia** i zapisywane do pamięci flash (NVS) — strojenie przeżywa
  restart
* Ikona statusu połączenia z Wi-Fi na LCD
* Nieblokująca obsługa sieci: brak Wi-Fi oznacza brak funkcji sieciowych, a nie martwą suszarkę
* Ogólna poprawa responsywności — koniec z migotaniem i gubionymi wciśnięciami przycisków

**Mechanika (planowana w tej wersji)**

* **Konstrukcja z podwójnymi ściankami** — lepsza izolacja, mniejsze zużycie energii i szybsze
  nagrzewanie
* **Otwory na przelot filamentu**, dzięki którym drukarka może pobierać filament bezpośrednio z
  suszarki w trakcie druku
* Dodatkowy mniejszy wentylator usprawniający pozbywanie się wilgoci

**W planach**

* MQTT i zdalne sterowanie
* Powiadomienia o zakończeniu suszenia — PC, telefon, e-mail

---

## v2 — poprawa wyświetlacza i grzania

* Wyświetlacz zmieniony z **20×2 na 20×4**, wraz z przeróbką kodu tak, żeby wszystkie wartości
  poprawnie rozłożyły się na większym ekranie
* Mocowanie ekranu przedrukowane z **ABS zamiast PETG** — znajduje się blisko ciepłej ścianki
  komory, a PETG w tym miejscu mięknął
* Nowa podstawa z **gęstszym układem otworów**, poprawiająca przepływ powietrza i skracająca czas
  nagrzewania
* Dodana **trzecia mata grzewcza** wraz z własnym radiatorem

<img src="../images/v2/screen_improvement.jpg" alt="Ulepszony wyświetlacz 20x4" width="500">

---

## v1 — pierwsza działająca suszarka

Pierwotna konstrukcja: drukowana rama z panelami z pleksi, ESP32-C3 na płytce prototypowej,
regulacja PID grzania, interfejs na LCD i timer suszenia — wszystko w jednym szkicu Arduino.

Zdjęcia z tego etapu znajdują się w [dzienniku budowy](dziennik-budowy.md).

Szkic v1 został zachowany w pliku
[`firmware/v1-arduino/filament_dryer_v1.ino`](../../firmware/v1-arduino/filament_dryer_v1.ino).
