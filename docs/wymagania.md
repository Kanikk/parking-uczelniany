# Wymagania

Każde wymaganie ma być atomowe, jednoznaczne, testowalne, niesprzeczne z pozostałymi i wykonalne. Dlatego kryteria akceptacji zawierają konkretne wartości, a jedno wymaganie opisuje jedną rzecz.

## 1. Od procesu do wymagań

Wymagania funkcjonalne wynikają z elementów [modelu procesu](proces/README.md): zadań, decyzji i wyjątków.

| Element procesu | W modelu procesu | Wymaganie |
|-----------------|------------------|-----------|
| Zadanie | Zakłada konto i loguje się | US-01 Założenie konta, US-02 Logowanie |
| Zadanie | Rejestruje pojazd | US-03 Rejestracja pojazdu |
| Zadanie | Weryfikuje kierowcę, nadaje uprawnienie | US-04 Nadanie uprawnienia |
| Decyzja | `[brak podstaw]` | US-04, kryterium: odmowa z powodem |
| Zadanie | Odczytuje tablicę, sprawdza uprawnienie | US-05 Automatyczny wjazd |
| Decyzja | `[brak miejsc]` | US-05, kryterium: szlaban zamknięty i komunikat; US-08 Podgląd wolnych miejsc |
| Wyjątek | `[tablica nierozpoznana lub brak uprawnienia]` | US-07 Ręczna obsługa wjazdu |
| Zadanie | Rejestruje wyjazd, zwalnia miejsce | US-06 Automatyczny wyjazd |

## 2. Wymagania funkcjonalne (user stories)

Priorytety według MoSCoW: **M** - Must have, **S** - Should have, **C** - Could have, **W** - Won't have (w tej wersji).

### Podsumowanie

| ID | User story (skrót) | Aktor | Priorytet |
|----|--------------------|-------|-----------|
| US-01 | Założenie konta | Kierowca | M |
| US-02 | Logowanie | Kierowca, ochrona, administrator | M |
| US-03 | Rejestracja pojazdu | Kierowca | M |
| US-04 | Nadanie uprawnienia do parkowania | Administrator | M |
| US-05 | Automatyczny wjazd | Kierowca | M |
| US-06 | Automatyczny wyjazd | Kierowca | M |
| US-07 | Ręczna obsługa wjazdu | Pracownik ochrony | M |
| US-08 | Podgląd wolnych miejsc | Kierowca | M |
| US-19 | Aplikacja bramowa | Urządzenia wjazdowe | M |
| US-09 | Zarządzanie strefami i miejscami | Administrator | S |
| US-10 | Przepustka jednorazowa dla gościa | Kierowca (pracownik), gość | S |
| US-11 | Podgląd zdarzeń na żywo | Pracownik ochrony | S |
| US-12 | Zarządzanie kontami i rolami | Administrator | S |
| US-13 | Historia własnych postojów | Kierowca | C |
| US-14 | Raporty zajętości | Administrator | C |
| US-15 | Logowanie kontem uczelnianym (SSO) | Kierowca | W |
| US-16 | Wniosek o abonament online | Kierowca | W |
| US-17 | Płatność online za abonament | Kierowca | W |
| US-18 | Powiadomienia e-mail | Kierowca | W |
| US-20 | Odczyt tablicy aparatem telefonu | Urządzenia wjazdowe | W |

### Must have

**US-01 - Założenie konta**
Jako kierowca chcę założyć konto, aby móc zarejestrować swój pojazd.

Kryteria akceptacji:
- Kierowca podaje imię, nazwisko, adres e-mail i hasło o długości co najmniej 10 znaków.
- Próba rejestracji na adres e-mail, który już istnieje w systemie, kończy się komunikatem błędu.
- Nowe konto otrzymuje rolę "kierowca".
- Hasło nie jest nigdzie zapisywane ani wyświetlane jawnym tekstem.

**US-02 - Logowanie**
Jako użytkownik (kierowca, pracownik ochrony, administrator) chcę zalogować się adresem e-mail i hasłem, aby korzystać z funkcji przypisanych do mojej roli.

Kryteria akceptacji:
- Po podaniu poprawnych danych użytkownik jest zalogowany i widzi widok właściwy dla swojej roli.
- Po podaniu błędnych danych wyświetlany jest ten sam komunikat niezależnie od tego, czy błędny był e-mail, czy hasło.
- Po 5 nieudanych próbach z rzędu logowanie na to konto jest blokowane na 15 minut.
- Niezalogowany użytkownik, który otworzy chronioną stronę, jest przekierowany do logowania.
- Po wylogowaniu chronione strony są niedostępne.

**US-03 - Rejestracja pojazdu**
Jako kierowca chcę zarejestrować swój pojazd, aby administrator mógł nadać mu uprawnienie, a system rozpoznawał go przy wjeździe.

Kryteria akceptacji:
- Kierowca podaje numer rejestracyjny, markę i model; numer jest walidowany (litery i cyfry, 4-8 znaków) i zapisywany wielkimi literami bez spacji.
- Próba dodania numeru, który jest już przypisany do innego konta, kończy się komunikatem błędu.
- Kierowca może mieć maksymalnie 2 aktywne pojazdy.
- Nowy pojazd ma status "oczekuje na uprawnienie" i jest widoczny dla administratora.
- Kierowca może usunąć pojazd, który nie ma aktywnego uprawnienia.

**US-04 - Nadanie uprawnienia do parkowania**
Jako administrator chcę nadać pojazdowi uprawnienie do parkowania, aby kontrolować, kto parkuje na terenie uczelni.

Kryteria akceptacji:
- Administrator widzi listę pojazdów oczekujących na uprawnienie z danymi kierowcy.
- Nadając uprawnienie, administrator wybiera strefę i datę końca ważności.
- System nie pozwala nadać uprawnienia, jeśli limit uprawnień w strefie został wyczerpany.
- Administrator może odmówić nadania uprawnienia, podając powód; kierowca widzi status i powód przy swoim pojeździe.
- Administrator może cofnąć aktywne uprawnienie; od tej chwili pojazd nie jest wpuszczany automatycznie.
- Każde nadanie, odmowa i cofnięcie jest zapisywane z identyfikatorem administratora i datą.

**US-05 - Automatyczny wjazd**
Jako kierowca z aktywnym uprawnieniem chcę, aby szlaban otwierał się po rozpoznaniu mojej tablicy, żeby wjechać bez zatrzymywania się przy portierni.

Kryteria akceptacji:
- Jeśli tablica jest rozpoznana, pojazd ma ważne uprawnienie i w strefie są wolne miejsca, szlaban podnosi się w czasie do 2 s od odczytu.
- System zapisuje sesję postoju (pojazd, strefa, czas wjazdu) i zmniejsza liczbę wolnych miejsc o 1.
- Przy braku wolnych miejsc szlaban pozostaje zamknięty, a urządzenie wjazdowe (aplikacja bramowa) wyświetla "Brak wolnych miejsc".
- Przy braku uprawnienia lub nierozpoznanej tablicy szlaban pozostaje zamknięty, a ochrona dostaje zgłoszenie (US-07).
- Pojazd, który ma już otwartą sesję postoju, nie tworzy drugiej sesji.

**US-06 - Automatyczny wyjazd**
Jako kierowca chcę, aby szlaban wyjazdowy otwierał się automatycznie, żeby sprawnie opuścić parking.

Kryteria akceptacji:
- Po odczycie tablicy pojazdu z otwartą sesją szlaban wyjazdowy podnosi się w czasie do 2 s.
- System zamyka sesję postoju (czas wyjazdu) i zwiększa liczbę wolnych miejsc o 1.
- Jeśli tablica nie zostanie rozpoznana, szlaban otwiera się po zatwierdzeniu przez ochronę, a zdarzenie jest oznaczane do wyjaśnienia.

**US-07 - Ręczna obsługa wjazdu**
Jako pracownik ochrony chcę ręcznie wpuścić lub odesłać pojazd, którego system nie wpuścił automatycznie, aby obsłużyć sytuacje wyjątkowe.

Kryteria akceptacji:
- Gdy automatyczny wjazd się nie powiedzie, w panelu ochrony w ciągu 3 s pojawia się zgłoszenie z odczytaną tablicą (lub informacją o braku odczytu) i przyczyną.
- Ochrona może wybrać "Wpuść" (z obowiązkowym powodem i możliwością poprawienia numeru tablicy) albo "Odmów".
- "Wpuść" podnosi szlaban i zapisuje sesję postoju oznaczoną jako wjazd ręczny, z identyfikatorem pracownika ochrony.
- "Odmów" zapisuje zdarzenie odmowy; szlaban pozostaje zamknięty.

**US-08 - Podgląd wolnych miejsc**
Jako kierowca chcę widzieć liczbę wolnych miejsc w strefach, aby przed przyjazdem wiedzieć, czy zaparkuję.

Kryteria akceptacji:
- Strona główna pokazuje liczbę wolnych i wszystkich miejsc dla każdej strefy.
- Wartość odświeża się bez przeładowania strony i jest opóźniona maksymalnie o 5 s względem stanu faktycznego.
- Strefa bez wolnych miejsc jest wyraźnie oznaczona.

**US-19 - Aplikacja bramowa**
Jako zespół projektowy chcemy mieć aplikację bramową uruchamianą w przeglądarce telefonu, aby odgrywać i testować wjazdy i wyjazdy bez kamery ANPR i sterownika szlabanu.

Kryteria akceptacji:
- Aplikacja ma dwa tryby, wjazd i wyjazd, wybierane przez użytkownika.
- Numer rejestracyjny wpisuje się ręcznie; jest walidowany i normalizowany tak jak w US-03.
- Po zatwierdzeniu aplikacja wyświetla decyzję systemu (szlaban podniesiony albo odmowa z przyczyną) oraz liczbę wolnych miejsc w strefie.
- Decyzję podejmuje ta sama logika, która obsługuje odczyt z kamery ANPR; zastąpienie aplikacji kamerą nie wymaga zmian w pozostałych serwisach.
- Interfejs działa na telefonie od 360 px szerokości (NFR-13).

### Should have

**US-09 - Zarządzanie strefami i miejscami**
Jako administrator chcę definiować strefy i ich limity miejsc, aby dopasować system do faktycznego układu parkingu.

Kryteria akceptacji:
- Administrator może dodać, edytować i dezaktywować strefę (nazwa, liczba miejsc).
- Zmniejszenie liczby miejsc poniżej liczby aktualnie zajętych jest blokowane z komunikatem.
- Administrator może ręcznie skorygować licznik zajętości strefy; korekta jest zapisywana w dzienniku zdarzeń.

**US-10 - Przepustka jednorazowa dla gościa**
Jako pracownik uczelni chcę zgłosić gościa, aby mógł wjechać na parking w dniu wizyty.

Kryteria akceptacji:
- Pracownik podaje numer rejestracyjny gościa, datę wizyty oraz imię i nazwisko gościa.
- W dniu wizyty pojazd gościa jest wpuszczany automatycznie tak jak pojazd z uprawnieniem; w inne dni przepustka nie działa.
- Pracownik może wystawić maksymalnie 3 przepustki na dzień.
- Pracownik może anulować przepustkę, dopóki gość nie wjechał.

**US-11 - Podgląd zdarzeń na żywo**
Jako pracownik ochrony chcę widzieć na żywo wjazdy i wyjazdy, aby mieć kontrolę nad tym, co dzieje się przy szlabanach.

Kryteria akceptacji:
- Panel pokazuje listę ostatnich zdarzeń (czas, tablica, kierunek, wynik) aktualizowaną bez odświeżania strony.
- Zdarzenia wymagające reakcji (US-07) są wyróżnione i wyświetlane na górze.
- Ochrona może wyszukać zdarzenia po numerze tablicy z ostatnich 30 dni.

**US-12 - Zarządzanie kontami i rolami**
Jako administrator chcę nadawać kontom role, aby pracownicy ochrony i inni administratorzy mieli dostęp do swoich paneli.

Kryteria akceptacji:
- Administrator może zmienić rolę konta na "kierowca", "pracownik", "ochrona" lub "administrator".
- Administrator może zablokować konto; zablokowany użytkownik nie może się zalogować.
- System nie pozwala odebrać roli ostatniemu aktywnemu administratorowi.
- Każda zmiana roli jest zapisywana z identyfikatorem administratora i datą.

### Could have

**US-13 - Historia własnych postojów**
Jako kierowca chcę widzieć historię swoich wjazdów i wyjazdów, aby móc sprawdzić, kiedy korzystałem z parkingu.

Kryteria akceptacji:
- Lista pokazuje sesje postoju kierowcy (data, strefa, czas wjazdu i wyjazdu) z ostatnich 12 miesięcy.
- Kierowca widzi wyłącznie sesje własnych pojazdów.

**US-14 - Raporty zajętości**
Jako administrator chcę generować raporty zajętości, aby planować liczbę uprawnień i podział na strefy.

Kryteria akceptacji:
- Raport pokazuje średnią i maksymalną zajętość stref w wybranym zakresie dat, w podziale na dni i godziny.
- Raport można wyeksportować do pliku CSV.

### Won't have (w tej wersji)

Te funkcje są świadomie odłożone. Architektura przewiduje ich dodanie bez przebudowy istniejących serwisów (patrz [architektura.md](architektura.md), punkt "Punkty rozszerzeń").

**US-15 - Logowanie kontem uczelnianym (SSO)**
Jako kierowca chciałbym logować się kontem uczelnianym, aby nie pamiętać osobnego hasła. Odłożone: wymaga integracji z systemem tożsamości uczelni.

**US-16 - Wniosek o abonament online**
Jako kierowca chciałbym sam złożyć wniosek o abonament na wybraną strefę i okres. Odłożone: w tej wersji uprawnienia nadaje administrator.

**US-17 - Płatność online za abonament**
Jako kierowca chciałbym opłacić abonament online. Odłożone: wymaga integracji z bramką płatności.

**US-18 - Powiadomienia e-mail**
Jako kierowca chciałbym dostać e-mail o nadaniu lub wygasaniu uprawnienia. Odłożone: w tej wersji status jest widoczny w portalu.

**US-20 - Odczyt tablicy aparatem telefonu**
Jako użytkownik aplikacji bramowej chciałbym, aby aplikacja odczytywała tablicę aparatem telefonu zamiast ręcznego wpisywania. Odłożone: w tej wersji numer wpisuje się ręcznie (US-19).

## 3. Wymagania pozafunkcjonalne

| ID | Obszar | Wymaganie | Sposób weryfikacji |
|----|--------|-----------|--------------------|
| NFR-01 | Wydajność | Decyzja o wjeździe lub wyjeździe (od odczytu tablicy do polecenia dla szlabanu) trwa do 2 s w 95% przypadków | Test obciążeniowy, pomiar percentyla 95 |
| NFR-02 | Wydajność | Odpowiedzi API dla portalu trwają do 500 ms w 95% żądań przy 200 jednocześnie zalogowanych użytkownikach | Test obciążeniowy |
| NFR-03 | Dostępność | Obsługa wjazdu i wyjazdu jest dostępna przez 99,5% czasu w godzinach 6:00-22:00 | Monitoring, raport miesięczny |
| NFR-04 | Niezawodność | Przy utracie połączenia z platformą serwis bramowy działa w trybie awaryjnym co najmniej 4 godziny: wpuszcza pojazdy z lokalnej listy uprawnień i zapisuje zdarzenia do późniejszej synchronizacji | Test: odłączenie sieci |
| NFR-05 | Niezawodność | Awaria `user-service` lub aplikacji webowej nie przerywa obsługi wjazdów i wyjazdów | Test: wyłączenie kontenera |
| NFR-06 | Bezpieczeństwo | Cała komunikacja z klientami odbywa się po HTTPS (TLS 1.2 lub nowszy) | Przegląd konfiguracji, skan TLS |
| NFR-07 | Bezpieczeństwo | Hasła są przechowywane wyłącznie jako skrót Argon2id | Przegląd kodu i bazy danych |
| NFR-08 | Bezpieczeństwo | Uprawnienia wynikające z roli są sprawdzane po stronie serwera przy każdym żądaniu API | Testy uprawnień dla każdej roli |
| NFR-09 | Bezpieczeństwo | Każde ręczne otwarcie szlabanu i każda decyzja administratora są zapisywane w dzienniku audytowym (kto, kiedy, co), którego wpisów nie można edytować z poziomu aplikacji | Testy, przegląd |
| NFR-10 | Prywatność (RODO) | Sesje postoju są anonimizowane po 12 miesiącach od daty wyjazdu | Test zadania czyszczącego |
| NFR-11 | Skalowalność | Każdy mikroserwis jest bezstanowy i może działać w co najmniej 2 instancjach jednocześnie bez zmian w kodzie | Test: uruchomienie 2 replik |
| NFR-12 | Rozszerzalność | Dodanie nowego serwisu, który odbiera istniejące zdarzenia, nie wymaga zmian w kodzie serwisów już działających | Test: podłączenie próbnego odbiorcy zdarzeń |
| NFR-13 | Użyteczność | Interfejs jest responsywny (od 360 px szerokości), w języku polskim i spełnia WCAG 2.1 na poziomie AA | Testy na urządzeniach, audyt dostępności |
| NFR-14 | Utrzymywalność | Każdy serwis ma testy automatyczne z pokryciem logiki biznesowej co najmniej 70%, uruchamiane w CI przy każdym pull requeście | Raport pokrycia w CI |
| NFR-15 | Obserwowalność | Serwisy zapisują logi w formacie JSON z identyfikatorem korelacji i udostępniają endpoint stanu zdrowia | Przegląd, test |
| NFR-16 | Przenośność | Całe środowisko uruchamia się lokalnie jednym poleceniem (`docker compose up`) na systemach Linux, macOS i Windows | Test na czystej maszynie |
| NFR-17 | Efektywność operacyjna | Co najmniej 90% wjazdów jest obsłużonych automatycznie, bez interwencji ochrony | Raport zdarzeń: udział wjazdów bez oznaczenia "wjazd ręczny" |
