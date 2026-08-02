# Dziennik budowy

[← powrót do README](../../README.pl.md) · [🇬🇧 English version](../en/build-log.md)

Zdjęcia z budowy wersji 1, mniej więcej w kolejności, w jakiej powstawała. Zmiany z wersji 2 są na
końcu.

---

## 1. Prototyp na płytce stykowej

Zanim cokolwiek powstało w CAD-zie, cała elektronika została złożona na blacie: ESP32-C3,
wyświetlacz, czujniki I²C i moduły MOSFET połączone luźnymi przewodami — tak, żeby sprawdzić
przypisanie pinów i odczyty czujników na działającym ekranie.

<img src="../images/build/prototyping.jpg" alt="Prototyp elektroniki na stole warsztatowym" width="620">

To z tego etapu bierze się mapa pinów w
[`config.h`](../../firmware/v3-freertos/include/config.h) — piny zostały wybrane właśnie tutaj i
później już się nie zmieniały.

---

## 2. Druk ramy

Obudowa nie jest drukowanym pudełkiem. To zestaw drukowanych narożników i profili krawędziowych,
które spinają panele z pleksi 300×300 mm — zdecydowanie mniej filamentu, dużo krótszy czas druku
i komora, do której faktycznie widać.

<img src="../images/build/printing_parts.jpg" alt="Elementy ramy na stole drukarki" width="620">

Każda część została narysowana w Autodesk Inventor; pliki źródłowe są w
[`hardware/v1/cad/`](../../hardware/v1/cad/).

---

## 3. Wydrukowane części

Komplet narożników, kątowników i łączników rozłożony przed montażem. Kilka części ma po kilka
wersji — te nieaktualne leżą w folderach `OldVersions/` zamiast zostać skasowane, żeby historia
projektowania pozostała widoczna.

<img src="../images/build/printed_parts.jpg" alt="Zestaw wydrukowanych elementów ramy" width="620">

---

## 4. Montaż obudowy

Rama i panele z pleksi składane w gotową komorę.

<img src="../images/build/enclosure.jpg" alt="Złożona obudowa" width="620">

---

## 5. Wnętrze komory

Rozplanowanie środka: gdzie usiądą maty grzewcze wraz z radiatorami, jak przechodzi obok nich
powietrze i jak podeprzeć szpule, żeby swobodnie się obracały.

<img src="../images/build/interior_concept.jpg" alt="Wnętrze komory" width="620">

---

## 6. Płytka sterownika i wyświetlacz

Elektronika przeniosła się ze stykówki na płytkę prototypową, a wyświetlacz trafił do drukowanego
mocowania na panelu przednim.

<img src="../images/build/screen_and_board.jpg" alt="Wyświetlacz i płytka sterownika" width="620">

**Film:** [przegląd płytki sterownika](../media/controller_board.mp4) *(2,6 MB, bez dźwięku)*

<a href="../media/controller_board.mp4">
  <img src="../images/build/controller_board_poster.jpg" alt="Kadr z filmu z płytką sterownika" width="480">
</a>

---

## 7. Bezpieczeństwo

250 W grzania pracujące bez nadzoru przez wiele godzin wymaga szacunku. Przewody zostały dobrane pod
prąd, maty dostały własne czujniki DS18B20, a firmware — twarde zabezpieczenie termiczne, które
zeruje setpoint przy 110 °C i nie pozwala wrócić do grzania, dopóki każda mata nie spadnie poniżej
70 °C. Zobacz [`control_task.cpp`](../../firmware/v3-freertos/src/tasks/control_task.cpp).

<img src="../images/build/safety_first.jpg" alt="Okablowanie i zabezpieczenia" width="620">

---

## 8. Gotowa wersja 1

<img src="../images/hero.jpg" alt="Gotowa suszarka" width="620">

---

## Wersja 2 — poprawa wyświetlacza

Wyświetlacz 20×2 został zastąpiony przez 20×4, co wymusiło przepisanie kodu obsługi ekranu tak, żeby
wszystkie wartości nadal trafiały w sensowne miejsca. Samo mocowanie zostało przedrukowane z **ABS**
zamiast PETG, bo znajduje się blisko ciepłej ścianki komory, a PETG okazał się tam zbyt miękki.

<img src="../images/v2/screen_improvement.jpg" alt="Nowe mocowanie wyświetlacza 20x4" width="620">

Wersja 2 przyniosła też nową podstawę z gęstszym układem otworów dla lepszego przepływu powietrza
oraz trzecią matę grzewczą z własnym radiatorem — razem wyraźnie skróciło to czas nagrzewania.
Szczegóły w [historii zmian](historia-zmian.md).
