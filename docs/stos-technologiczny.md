# Stos technologiczny

Założenia zespołu: architektura mikroserwisowa, głównym językiem jest **TypeScript**, a **Go** używamy tam, gdzie daje wyraźną korzyść. Pierwsza wersja ma jak najmniej elementów, ale każdy odłożony element da się dołożyć bez przebudowy.

## 1. Zestawienie

| Warstwa | Technologia | Uzasadnienie |
|---------|-------------|--------------|
| Język główny | **TypeScript** (Node.js LTS) | Jeden język na frontendzie i backendzie: wspólne typy i walidacja, łatwiejsze recenzje kodu między członkami zespołu, statyczne typowanie ogranicza błędy na styku serwisów |
| Język dodatkowy | **Go** (tylko `gate-service`) | Serwis bramowy ma docelowo utrzymywać wiele równoległych połączeń z urządzeniami i działać na słabym komputerze przy szlabanie: Go daje lekką współbieżność, małe zużycie pamięci i pojedynczy plik wykonywalny bez środowiska uruchomieniowego |
| Frontend | **Next.js + React** | Jedna aplikacja dla trzech ról i widoku bramowego, routing w standardzie, duży ekosystem |
| Style | **Tailwind CSS** | Szybkie budowanie responsywnego interfejsu (NFR-13) bez utrzymywania własnych arkuszy stylów |
| Framework serwisów | **NestJS** | Narzuca jednolitą strukturę wszystkich serwisów (moduły, wstrzykiwanie zależności), ma gotowe integracje z RabbitMQ, WebSocket i OpenAPI |
| API synchroniczne | **REST + OpenAPI** | Prosty, powszechnie znany styl |
| Komunikacja asynchroniczna | **RabbitMQ** | Dojrzały broker z potwierdzeniami i ponawianiem dostarczenia; dobre biblioteki dla Node.js i Go |
| Dane na żywo | **WebSocket** (w API Gateway) | Licznik wolnych miejsc i panel ochrony wymagają aktualizacji bez odświeżania strony (US-08, US-11) |
| Baza danych | **PostgreSQL** (osobna baza per serwis) | Dane są relacyjne (konto, pojazd, uprawnienie, sesja), potrzebne są transakcje i ograniczenia unikalności; transakcja wystarcza też do bezpiecznego zmniejszania licznika miejsc |
| ORM | **Prisma** | Schemat i migracje w repozytorium, klient w pełni typowany w TypeScript |
| Logowanie | **Passport + JWT**, hasła **Argon2id** | Własne konta bez zależności od systemów uczelni; Passport pozwala później dodać logowanie OIDC jako kolejną strategię (US-15) |
| Walidacja | **Zod** | Jedna definicja schematu daje walidację w czasie działania i typ TypeScript; schematy współdzielone między frontendem a serwisami |
| Konteneryzacja | **Docker + Docker Compose** | Każdy serwis w osobnym kontenerze, całe środowisko uruchamiane jednym poleceniem (NFR-16) |
| Repozytorium | **Monorepo: pnpm workspaces + Turborepo** | Wszystkie serwisy i wspólne pakiety (typy zdarzeń, schematy) w jednym repozytorium: jeden pull request może spójnie zmienić kontrakt i jego użycia |
| CI | **GitHub Actions** | Wbudowane w GitHub; lint, testy i budowanie obrazów przy każdym pull requeście (NFR-14) |
| Testy | **Vitest**, **Playwright**, pakiet `testing` w Go | Testy jednostkowe i integracyjne serwisów, testy end-to-end kluczowych ścieżek w przeglądarce, standardowe narzędzie testowe Go |
| Jakość kodu | **ESLint + Prettier**, `gofmt` + `golangci-lint` | Jednolity styl kodu bez dyskusji na recenzjach |
| Logi | **Pino** (logi JSON) | Logi strukturalne z identyfikatorem korelacji pozwalają prześledzić żądanie przez kilka serwisów (NFR-15) |
| Diagramy | **draw.io (diagrams.net)** | Darmowe, działa w przeglądarce, pliki `.drawio` przechowujemy w repozytorium obok eksportu PNG |

## 2. Dlaczego mikroserwisy

- **Izolacja awarii.** Obsługa szlabanów musi działać nawet wtedy, gdy nie działa portal lub logowanie (NFR-05).
- **Różne wymagania części systemu.** Ścieżka wjazdu wymaga krótkiego czasu odpowiedzi i pracy offline, a portal jest typową aplikacją formularzową.
- **Rozszerzalność.** Płatności, powiadomienia i wnioski dojdą jako osobne serwisy lub moduły, bez ruszania tego, co już działa.
- **Podział pracy.** Czteroosobowy zespół może rozwijać serwisy równolegle przy jasno określonych kontraktach.

Świadomie przyjmowane koszty: bardziej złożona infrastruktura, spójność ostateczna danych między serwisami i trudniejsze debugowanie. Ograniczamy je przez małą liczbę serwisów na start, monorepo, wspólne pakiety typów i Docker Compose.

## 3. Rozważane alternatywy

| Decyzja | Alternatywa | Dlaczego odrzucona lub odłożona |
|---------|-------------|---------------------------------|
| Własne logowanie | Keycloak i SSO uczelni | Odłożone: wymaga dostępu do systemu tożsamości uczelni i dodatkowego kontenera |
| Licznik miejsc w PostgreSQL | Redis | Odłożone: przy skali jednego parkingu transakcja w bazie wystarcza, a Redis to kolejny element do utrzymania |
| NestJS | Express / Fastify bez frameworka | Przy kilku serwisach i czterech osobach brak narzuconej struktury prowadzi do rozbieżnych konwencji |
| RabbitMQ | Apache Kafka | Zbyt ciężka w utrzymaniu przy tej skali; nie potrzebujemy trwałego logu zdarzeń ani bardzo dużej przepustowości |
| REST | gRPC | Lepsza wydajność nie jest tu potrzebna, a REST jest prostszy w debugowaniu i wspólny dla przeglądarki i serwisów |
| PostgreSQL | MongoDB | Dane są silnie relacyjne i wymagają transakcji oraz ograniczeń unikalności |
