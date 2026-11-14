# Parking uczelniany

System do zarządzania parkingiem uczelni: rejestracja pojazdów i uprawnienia do parkowania, automatyczny wjazd i wyjazd po rozpoznaniu tablicy rejestracyjnej, bieżąca liczba wolnych miejsc.

## Zespół

| Osoba | Rola |
|-------|------|
| Kamil Urbaś | lider zespołu |
| Filip Urbańczyk | członek zespołu |
| Adrian Wolf | członek zespołu |
| Gabriel Lorenc | członek zespołu |

## Dokumentacja

| Dokument | Zawartość |
|----------|-----------|
| [docs/wizja.md](docs/wizja.md) | Kontekst projektu: wizja systemu, zakres, aktorzy, główny scenariusz |
| [docs/proces/](docs/proces/README.md) | Model głównego procesu biznesowego |
| [docs/wymagania.md](docs/wymagania.md) | User stories z kryteriami akceptacji i MoSCoW, wymagania pozafunkcjonalne |
| [docs/przypadki-uzycia.md](docs/przypadki-uzycia.md) | Diagram przypadków użycia i opis najważniejszych przypadków |
| [docs/architektura.md](docs/architektura.md) | Diagram kontenerów C4 (mikroserwisy) |
| [docs/stos-technologiczny.md](docs/stos-technologiczny.md) | Stos technologiczny z uzasadnieniem |


## Zasady pracy

1. Nikt nie commituje bezpośrednio do `main`.
2. Każda zmiana powstaje na gałęzi roboczej `feature/opis-zmiany`, np. `feature/model-procesu`.
3. Z gałęzi otwierany jest pull request.
4. Review robi inna osoba z zespołu niż autor.
5. Po akceptacji następuje merge do `main`.
6. Każdy członek zespołu ma co najmniej jeden zmergowany pull request.
