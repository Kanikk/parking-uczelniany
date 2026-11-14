# Wizja i kontekst projektu

## 1. Wizja systemu

**Parking uczelniany** to system informatyczny, który zarządza dostępem do parkingów uczelni: od zarejestrowania pojazdu i nadania mu uprawnienia, przez automatyczny wjazd i wyjazd na podstawie tablicy rejestracyjnej, po bieżącą informację o wolnych miejscach.

Architektura ma pozwalać na dalszy rozwój i testowanie bez fizycznych urządzeń przy szlabanie.

Ponieważ zespół nie ma dostępu do kamery ANPR ani sterownika szlabanu, ich rolę pełni aplikacja bramowa uruchamiana w przeglądarce telefonu. Działa w dwóch trybach, wjazd i wyjazd, a numer rejestracyjny wpisuje się ręcznie (docelowo opcjonalnie odczytuje go aparat telefonu). Dzięki temu każdy scenariusz wjazdu i wyjazdu można odegrać i przetestować na zwykłym telefonie, a podłączenie prawdziwej kamery w przyszłości nie wymaga zmian w pozostałej części systemu.

W systemie:
- **kierowca** rejestruje pojazd online i wjeżdża bez zatrzymywania się przy portierni,
- **ochrona** zajmuje się tylko sytuacjami nietypowymi zamiast każdym pojazdem,
- **administrator** ma jedno miejsce do nadawania uprawnień, zarządzania strefami i limitem miejsc.

Pierwsza wersja jest celowo mała. Elementy odłożone na później są wymienione w punkcie 3 i mają przygotowane miejsce w architekturze.

## 2. Cele

| Cel | Miernik |
|-----|---------|
| Skrócenie wjazdu na parking | decyzja o podniesieniu szlabanu w czasie do 2 s od odczytu tablicy |
| Odciążenie ochrony | co najmniej 90% wjazdów obsłużonych automatycznie |
| Dostępna informacja o miejscach | liczba wolnych miejsc widoczna online, opóźnienie do 5 s |
| Koniec papierowej listy pojazdów | 100% uprawnień prowadzonych w systemie |

## 3. Zakres

**W zakresie pierwszej wersji:**

- własne konta użytkowników (e-mail i hasło),
- rejestracja pojazdów przez kierowców,
- ręczne nadawanie uprawnień do parkowania przez administratora,
- automatyczny wjazd i wyjazd (odczyt tablicy, sterowanie szlabanem),
- ręczna obsługa wjazdu przez ochronę w sytuacjach wyjątkowych,
- jednorazowe przepustki dla gości,
- licznik wolnych miejsc w podziale na strefy,
- panel administratora (uprawnienia, strefy, konta).

**Odłożone na kolejne wersje (architektura to przewiduje):**

- logowanie kontem uczelnianym (SSO),
- wnioski o abonament składane online przez kierowców,
- płatności online za abonament,
- powiadomienia e-mail.

**Poza zakresem projektu:**

- natywna aplikacja mobilna (zastępuje ją responsywna aplikacja webowa),
- opłaty godzinowe za postój i parkomaty,
- własny algorytm rozpoznawania tablic - system korzysta z dostarczonego odczytu (aplikacji bramowej, a docelowo kamery ANPR, szlaban, tablica LED).

## 4. Aktorzy

### Aktorzy główni (ludzie)

| Aktor | Opis | Co robi w systemie |
|-------|------|--------------------|
| **Kierowca** (student lub pracownik uczelni) | Osoba, która chce parkować na terenie uczelni | Zakłada konto, rejestruje pojazd, wjeżdża i wyjeżdża, sprawdza wolne miejsca. Pracownik może dodatkowo zgłosić gościa |
| **Gość** | Osoba bez konta, wjeżdżająca jednorazowo (np. prelegent, dostawca) | Wjeżdża i wyjeżdża na podstawie przepustki jednorazowej albo po weryfikacji przez ochronę |
| **Pracownik ochrony** | Osoba dyżurująca przy wjeździe | Obsługuje sytuacje wyjątkowe (nierozpoznana tablica, brak uprawnienia), ręcznie otwiera szlaban, widzi bieżące zdarzenia |
| **Administrator parkingu** | Pracownik administracji uczelni odpowiedzialny za parking | Nadaje i cofa uprawnienia, zarządza strefami, limitem miejsc i rolami kont |

### Aktor wspierający (system zewnętrzny)

| Aktor | Opis | Co robi |
|-------|------|---------|
| **Urządzenia wjazdowe** | aplikacja bramowa na telefonie (docelowo kamera ANPR, szlaban, tablica LED) | Odczytują tablicę rejestracyjną, podnoszą szlaban, wyświetlają liczbę wolnych miejsc |

## 5. Główny scenariusz

Skrót głównego procesu; pełny model znajduje się w [proces/](proces/README.md).

| Krok | Czynność | Kto |
|------|----------|-----|
| 1 | Zakłada konto i loguje się | Kierowca |
| 2 | Rejestruje pojazd | Kierowca |
| 3 | Weryfikuje kierowcę i nadaje uprawnienie | Administrator parkingu |
| 4 | Aktywuje uprawnienie | System |
| 5 | Odczytuje tablicę przy wjeździe | Urządzenia wjazdowe |
| 6 | Sprawdza uprawnienie i wolne miejsca | System |
| 7 | Przy braku odczytu lub uprawnienia weryfikuje kierowcę ręcznie | Pracownik ochrony |
| 8 | Podnosi szlaban, rejestruje wjazd | Urządzenia wjazdowe, system |
| 9 | Przy wyjeździe rejestruje wyjazd i zwalnia miejsce | Urządzenia wjazdowe, system |

## 6. Słownik pojęć

| Pojęcie | Znaczenie |
|---------|-----------|
| Uprawnienie | Prawo pojazdu do parkowania w określonej strefie przez określony czas, nadawane przez administratora |
| Przepustka jednorazowa | Uprawnienie gościa do wjazdu w konkretnym dniu, przypisane do tablicy rejestracyjnej |
| Strefa | Wydzielona część parkingu z własnym limitem miejsc (np. strefa pracownicza, studencka) |
| Sesja postoju | Zapis jednego pobytu pojazdu: czas wjazdu, czas wyjazdu, strefa |
| Aplikacja bramowa | Aplikacja webowa uruchamiana na telefonie. Przekazuje do systemu numer rejestracyjny (wpisany ręcznie, opcjonalnie odczytany aparatem) i wyświetla decyzję. Zastępuje kamerę ANPR, szlaban i tablicę LED |
| ANPR | Automatic Number Plate Recognition - automatyczne rozpoznawanie tablic rejestracyjnych |
