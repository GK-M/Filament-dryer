# Plany rozwoju

[← powrót do README](../../README.pl.md) · [🇬🇧 English version](../en/roadmap.md)

---

## Zrobione

- [x] Timer suszenia
- [x] Zabezpieczenie termiczne — zerowanie wartości zadanej po przekroczeniu limitu na macie
- [x] Osobny task wyświetlacza
- [x] Aktualizacja firmware'u przez Wi-Fi (ArduinoOTA)
- [x] Nastawy PID edytowalne z urządzenia i zapisywane do pamięci flash
- [x] Ikona statusu Wi-Fi na LCD

## Następne w kolejce

- [ ] **Sterowanie wentylatorem od wilgotności** — zastąpienie obecnego progu prawdziwym prawem
      regulacji
- [ ] **Obsługa LED** — przytrzymanie *Enter* włącza i wyłącza podświetlenie ekranu razem z LED-em
      komory
- [ ] **Odliczanie do schłodzenia** — gdy `pid_data.error` jest ustawione, pokazywanie czasu
      pozostałego do zejścia mat poniżej progu bezpieczeństwa zamiast statycznego ekranu awarii
- [ ] **Ekran awarii** — dokończenie układu `TEMP_ERROR`
- [ ] **Tryby pracy** — presety dla typów filamentu (PLA / PETG / ABS / Nylon) pod przyciskiem
      `Tryb`, który obecnie tylko przewija nieużywany enum

## Później

- [ ] **MQTT albo czyste TCP/IP** — raportowanie stanu na serwer
- [ ] **Zdalne sterowanie** — ustawianie temperatury i czasu spoza urządzenia
- [ ] **Powiadomienia o zakończeniu** — PC, telefon, e-mail
- [ ] **Komora z podwójnymi ściankami** — lepsza izolacja, szybsze nagrzewanie, niższe zużycie
      energii
- [ ] **Przelot na filament** — możliwość pobierania filamentu przez drukarkę wprost z suszarki
- [ ] **Drugi, mniejszy wentylator** — lepsze pozbywanie się wilgoci

---

## Znane problemy

Rzeczy, które są obecnie błędne lub niedokończone — wypisane, a nie ukryte.

| # | Gdzie | Problem |
|:--:|---|---|
| 1 | `display_task.cpp`, `Screen::TEMP_ERROR` | Uszkodzone ciągi formatujące (`"%5.1%d"` z argumentem typu `float`). Ekran awarii wypisze śmieci — ten ekran jest niedokończony. |
| 2 | `sensor_task.cpp` | W linii logu z AHT10 argumenty są zamienione: temperatura trafia w miejsce opisane jako wilgotność i odwrotnie. Kosmetyka, ale myli przy czytaniu logów. |
| 3 | `config.h`, `adrr_DS::addr2` | Zbłąkany `\` na końcu linii przenosi kolejny komentarz, przez co notatka `//Temp podzespołów` zostaje połknięta. Nieszkodliwe, ale nie powinno tam być. |
| 4 | `rtos_handles.h`, `enum class Screen` | `COUNT` znajduje się w środku enuma, więc `Enter` przewija tylko cztery pierwsze ekrany. Działa, ale jest kruche — dodanie ekranu w złym miejscu po cichu zmieni nawigację. |
| 5 | `fan_task.cpp` | Sterowanie wentylatorem to próg dwustanowy przy 20 % RH (wypełnienie 1023 powyżej, 800 poniżej), a nie regulacja od wilgotności obiecywana w interfejsie. `Screen::PID_fan` to puste miejsce zarezerwowane. |
| 6 | `led_task.cpp` | Task ustawia LED na pełną jasność i dalej kręci pustą pętlę. Sterowanie LED-em i podświetleniem przez długie przytrzymanie nie jest zaimplementowane. |
| 7 | `button_task.cpp` | Timer suszenia startuje niejawnie przy pierwszym dodaniu czasu, a nie przy świadomym uruchomieniu. Ustawienie czasu i odejście od urządzenia niechcący rozpoczyna grzanie. |
| 8 | `control_task.cpp` | `pid.SetTunings()` wywoływane jest co 10 ms. Przy tej bibliotece nieszkodliwe, ale to praca wykonywana w każdej iteracji dla wartości zmieniającej się tylko przy naciśnięciu przycisku. |
| 9 | cały projekt | Brak testów automatycznych. Katalog `test/` to pusty szkielet PlatformIO. |

Zgłoszenia błędów i pull requesty mile widziane — załóż issue.
