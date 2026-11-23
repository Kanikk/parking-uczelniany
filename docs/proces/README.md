# Model głównego procesu biznesowego

**Proces:** od rejestracji pojazdu do wyjazdu z parkingu
**Technika:** diagram aktywności UML z partycjami (torami) dla aktorów
**Pliki:** źródło [model-procesu.drawio](model-procesu.drawio), obraz [model-procesu.png](model-procesu.png)

![Diagram aktywności - główny proces biznesowy](model-procesu.png)

## 1. Legenda

Legenda znajduje się również na samym diagramie.

| Symbol | Znaczenie |
|--------|-----------|
| Czarne koło | Węzeł początkowy (jeden, w torze Kierowca) |
| Czarne koło w obwódce | Węzeł końcowy |
| Prostokąt z zaokrąglonymi rogami | Akcja (czynność wykonywana przez aktora z danego toru) |
| Romb z jednym wejściem i kilkoma wyjściami | Decyzja - warunki (wartownicy) w [nawiasach] na strzałkach wychodzących |
| Romb z kilkoma wejściami i jednym wyjściem | Scalenie ścieżek |
| Strzałka z otwartym grotem | Przepływ sterowania |
| Kolor czerwony | Ścieżka sytuacji wyjątkowej |
| Tor (partycja) niebieski | Aktor - użytkownik parkingu (Gość, Kierowca) |
| Tor (partycja) pomarańczowy | Aktor - personel (Administrator parkingu, Pracownik ochrony) |
| Tor (partycja) fioletowy | Aktor - Urządzenia wjazdowe (aplikacja bramowa) |
| Tor (partycja) zielony | Projektowany system (nie jest aktorem) |

Diagram ma osobny tor dla każdego z 5 aktorów z diagramu przypadków użycia (Gość, Kierowca, Urządzenia wjazdowe, Administrator parkingu, Pracownik ochrony) oraz tor projektowanego systemu.

## 2. Opis przebiegu

Proces ma jeden węzeł początkowy w torze Kierowca. Pierwsza decyzja (sposób uzyskania prawa wjazdu) rozdziela ścieżki: `[rejestracja własnego pojazdu]` prowadzi przez etap 1, `[zaproszenie gościa]` przez ścieżkę gościa. Gościa zaprasza Kierowca z rolą pracownika uczelni (UC-09) - nie ma osobnego toru "Pracownik uczelni".

**Etap 1 - uzyskanie uprawnienia (kierowca)**

1. Kierowca zakłada konto i loguje się.
2. Kierowca rejestruje pojazd, podając numer rejestracyjny.
3. System zapisuje pojazd i dodaje go do listy oczekujących na uprawnienie.
4. Administrator weryfikuje kierowcę i pojazd.
5. Jeśli kierowca ma prawo parkować, administrator nadaje uprawnienie (strefa, okres ważności), a system je aktywuje. W przeciwnym razie system zapisuje odmowę z powodem i proces się kończy.

**Ścieżka gościa - przepustka jednorazowa**

- Kierowca zgłasza gościa, podając numer rejestracyjny, datę wizyty oraz imię i nazwisko gościa.
- System wystawia przepustkę jednorazową ważną w dniu wizyty.

**Etap 2 - wjazd**

6. Kierowca (z uprawnieniem) albo gość (z przepustką) podjeżdża pod szlaban wjazdowy, każdy w swoim torze. Obie ścieżki scalają się przed urządzeniem wjazdowym.
7. Urządzenie wjazdowe przekazuje numer rejestracyjny (aplikacja bramowa w trybie "wjazd": numer wpisany ręcznie; docelowo odczyt z kamery ANPR).
8. System sprawdza, czy pojazd ma ważne uprawnienie lub przepustkę jednorazową. Ścieżka automatyczna i ścieżka po ręcznej weryfikacji przez ochronę scalają się przed kolejną akcją.
9. System sprawdza, czy w strefie są wolne miejsca.
10. Szlaban się podnosi, system rejestruje wjazd i zmniejsza liczbę wolnych miejsc.

**Etap 3 - postój i wyjazd**

11. Na podstawie rodzaju wjazdu system kieruje przebieg do toru Kierowca (`[wjazd na uprawnienie]`) albo Gość (`[wjazd na przepustkę]`).
12. Kierowca lub gość parkuje, a po zakończeniu postoju podjeżdża pod szlaban wyjazdowy. Obie ścieżki scalają się przed urządzeniem wjazdowym.
13. Urządzenie wjazdowe przekazuje numer (aplikacja bramowa w trybie "wyjazd") i podnosi szlaban, a system rejestruje wyjazd i zwalnia miejsce.

## 3. Decyzje

| Decyzja | Tor | Warunek i dalszy przebieg | Warunek i dalszy przebieg |
|---------|-----|---------------------------|---------------------------|
| Sposób uzyskania prawa wjazdu | Kierowca | `[rejestracja własnego pojazdu]` - założenie konta i rejestracja pojazdu (etap 1) | `[zaproszenie gościa]` - zgłoszenie gościa przez kierowcę i przepustka jednorazowa |
| Wynik weryfikacji kierowcy | Administrator parkingu | `[kierowca uprawniony]` - nadanie uprawnienia | `[brak podstaw]` - zapis odmowy, proces się kończy |
| Weryfikacja pojazdu przy wjeździe | System parkingowy | `[tablica rozpoznana, uprawnienie ważne]` - sprawdzenie wolnych miejsc | `[tablica nierozpoznana lub brak uprawnienia]` - **sytuacja wyjątkowa**, sprawę przejmuje ochrona |
| Decyzja ochrony | Pracownik ochrony | `[wjazd dozwolony]` - powrót do głównej ścieżki | `[odmowa]` - proces się kończy |
| Dostępność miejsc | System parkingowy | `[wolne miejsca]` - szlaban się podnosi | `[brak miejsc]` - komunikat "Brak wolnych miejsc", proces się kończy |
| Rodzaj wjazdu | System parkingowy | `[wjazd na uprawnienie]` - postój i wyjazd w torze Kierowca | `[wjazd na przepustkę]` - postój i wyjazd w torze Gość |

Scalenia (romby z kilkoma wejściami i jednym wyjściem) występują w trzech miejscach: przed przekazaniem numeru przy wjeździe (Kierowca i Gość), przed sprawdzeniem wolnych miejsc (ścieżka automatyczna i decyzja ochrony) oraz przed przekazaniem numeru przy wyjeździe (Kierowca i Gość).

## 4. Sytuacja wyjątkowa

**Nierozpoznana tablica lub brak ważnego uprawnienia** (na diagramie kolor czerwony). Przyczyną może być błędnie wpisany numer lub zabrudzona tablica i błąd kamery, wygasłe uprawnienie albo gość bez przepustki. Automatyczna ścieżka zostaje przerwana, a pracownik ochrony weryfikuje kierowcę ręcznie (np. sprawdza legitymację lub listę gości). Jeśli uzna wjazd za dozwolony, proces wraca do głównej ścieżki, a zdarzenie jest zapisywane jako wjazd ręczny. W przeciwnym razie ochrona odmawia wjazdu i odnotowuje zdarzenie.

## 5. Udział aktorów

| Aktor | Tor | Udział w procesie |
|-------|-----|-------------------|
| Kierowca | Kierowca | Konto, rejestracja pojazdu, zgłoszenie gościa, wjazd, postój, wyjazd |
| Gość | Gość | Wjazd, postój i wyjazd na podstawie przepustki jednorazowej (bez etapu 1) |
| Urządzenia wjazdowe | Urządzenia wjazdowe | Przekazanie numeru rejestracyjnego, podniesienie szlabanu (aplikacja bramowa na telefonie) |
| Administrator parkingu | Administrator parkingu | Weryfikacja kierowcy i nadanie uprawnienia |
| Pracownik ochrony | Pracownik ochrony | Obsługa sytuacji wyjątkowej przy wjeździe |

System parkingowy ma własny tor, ale nie jest aktorem - to projektowany system.
