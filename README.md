# Koło Fortuny (C++)

Konsolowa gra **„Koło Fortuny”** w języku C++, realizowana jako projekt zaliczeniowy z przedmiotu **Podstawy Programowania** (UKW).

> **Status projektu:** w trakcie budowy (szkielet repozytorium — implementacja krok po kroku przez Pull Requesty).

---

## Opis programu

Program umożliwia rozegranie wielorundowej gry konsolowej dla 1–3 graczy wzorowanej na teleturnieju „Koło Fortuny”.

Główne założenia gry:
- konfiguracja liczby graczy (`1–3`), ich imion oraz liczby rund (`1–10`),
- baza co najmniej 15 różnych haseł w minimum 3 kategoriach (`map<string, vector<string>>`) bez powtarzania tego samego hasła w jednej grze,
- losowanie pól koła (`100`–`1000` pkt, `STOP`, `BANKRUT`),
- odgadywanie pojedynczych liter (punkty: `wartość pola × liczba wystąpień`) lub próba odgadnięcia całego hasła,
- przenoszenie zdobytych punktów między rundami,
- walidacja wszystkich danych wejściowych (odporność na błędne opcje, tekst zamiast liczby, powtórzone lub wieloznakowe litery),
- podsumowanie wyników i automatyczne wskazanie zwycięzcy (lub remisu).

---

## Przyjęte założenia projektowe

Poniższe decyzje zostały przyjęte świadomie w celu zapewnienia stabilności, przenośności między systemami (macOS / Linux / Windows) i przejrzystości kodu:

1. **Hasła i odgadywane litery w ASCII (`A–Z`, bez polskich znaków diakrytycznych):**
   - W projekcie wykorzystujemy typ `char` (1 bajt). W kodowaniu UTF-8 polskie znaki diakrytyczne (`Ą`, `Ć`, `Ę`, `Ł`, `Ń`, `Ó`, `Ś`, `Ź`, `Ż`) zajmują 2 bajty i nie mieszczą się w pojedynczym `char`.
   - Interfejs i komunikaty programu są w języku polskim, natomiast hasła i zgadywane litery zapisujemy bez polskich znaków (np. `LIGA MISTRZOW`).
   - Program nie rozróżnia wielkości liter — wpisy gracza są normalizowane do wielkich liter (`a` → `A`).
2. **Remis na końcu gry:** jeżeli kilku graczy uzyska taką samą najwyższą liczbę punktów, program wykrywa remis i wypisuje wszystkich zwycięzców ex aequo.
3. **Kolejność rozpoczynania rund:** każdą kolejną rundę rozpoczyna gracz nr 1 (punkty z poprzednich rund pozostają zachowane).
4. **Powtórzona litera:** wpisanie litery już użytej w bieżącej rundzie wyświetla komunikat i prosi o podanie innej litery w tej samej turze (bez utraty kolejki i bez ponownego kręcenia kołem).
5. **Odgadnięcie całego hasła:** poprawne odgadnięcie natychmiast kończy rundę bez dodatkowego bonusu punktowego (punkty zdobywa się przez trafione litery; architektura pozwala łatwo dodać stałą bonusową).
6. **Przedwczesne zakończenie gry (`3. Zakończ grę`):** kończy rozgrywkę i wyświetla podsumowanie dotychczasowych wyników, lidera (lub remis) oraz liczbę rozegranych rund.
7. **Organizacja kodu:** cały kod źródłowy znajduje się w jednym, czytelnie podzielonym na funkcje pliku `src/main.cpp` (minimum 6 własnych funkcji, bez OOP).

---

## Struktura repozytorium

```text
kolo-fortuny-cpp/
├── src/
│   └── main.cpp    # kod źródłowy gry (dodawany etapami)
├── .gitignore
└── README.md
```

---

## Kompilacja

Wymagany kompilator wspierający standard **C++17** (`g++` lub `clang++`).

### Linux / Windows (MinGW / g++)
```bash
g++ -std=c++17 -Wall -Wextra -Wpedantic src/main.cpp -o kolo_fortuny
```

### macOS (Apple Clang)
```bash
clang++ -std=c++17 -Wall -Wextra -Wpedantic src/main.cpp -o kolo_fortuny
```

---

## Uruchomienie

### macOS / Linux
```bash
./kolo_fortuny
```

### Windows
```powershell
.\kolo_fortuny.exe
```

---

## Obsługa gry

1. Po uruchomieniu programu podaj liczbę graczy (`1–3`), ich imiona oraz liczbę rund (`1–10`).
2. Na początku każdej rundy program losuje kategorię i ukryte hasło (spacje między wyrazami są widoczne).
3. W swojej turze gracz wybiera akcję z menu:
   - `1` — **Zakręć kołem** (losowanie pola punktowego, `STOP` lub `BANKRUT`),
   - `2` — **Odgadnij całe hasło** (wpisanie pełnego rozwiązania bez polskich znaków),
   - `3` — **Zakończ grę** (zakończenie rozgrywki i wyświetlenie podsumowania).
4. Poprawna litera odkrywa wszystkie jej wystąpienia, dolicza punkty i pozwala graczowi kontynuować turę. Błędna litera, pole `STOP`, pole `BANKRUT` (zerujące punkty) lub błędne hasło przekazują kolejkę kolejnemu graczowi.
5. Gra kończy się po rozegraniu wszystkich rund (lub wyborze opcji `3`) i wyświetla tabelę wyników oraz zwycięzcę.
