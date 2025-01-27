# Audyt Bezpieczeństwa Aplikacji "Bakery Management System Using JAVA and NetBeans IDE and SQL"

## Autor aplikacji: Shakir Ali
Link do projektu: [GitHub Repository](https://github.com/Shakir-Ali/Bakery-Management-System-Using-JAVA-and-Netbeans-IDE-and-SQL)

---

## Funkcjonalności aplikacji:

### Administrator:
- Może manipulować stanem zapasów produktów.
- Może zarządzać danymi kasjerów, w tym dodawaniem, edytowaniem i usuwaniem rekordów.
- Może przeglądać raporty sprzedaży, zapewniając wgląd w działalność piekarni.

### Kasjer:
- Ma możliwość generowania rachunków sprzedaży na podstawie wprowadzonych danych.
- Otrzymuje ograniczony dostęp do danych i nie ma uprawnień do zmiany zapasów ani szczegółów kasjerów.

---

## Technologie wykorzystane do stworzenia aplikacji:

1. **Język programowania**: Java
2. **Framework/IDE**: NetBeans IDE
3. **Baza danych**: MySQL (używana do przechowywania danych związanych z użytkownikami, produktami i transakcjami).
4. **Interfejs użytkownika**: Swing (wykorzystany do stworzenia graficznego interfejsu użytkownika).
5. **JDBC (Java Database Connectivity)**: Wykorzystywane do łączenia aplikacji z bazą danych MySQL, umożliwiając wykonywanie zapytań SQL.

---

## Zidentyfikowane problemy i rekomendacje

### 1. Brak walidacji danych wejściowych

#### Problem:
W aplikacji brak jest walidacji danych wprowadzanych przez użytkownika. Może to prowadzić do błędów, nadużyć i potencjalnych ataków, takich jak SQL Injection.

#### Sugestie:
- Warto rozważyć wprowadzenie walidacji danych wejściowych, np.:
  - Sprawdzanie, czy numery telefonów składają się wyłącznie z cyfr i mają odpowiednią długość.
  - Weryfikowanie adresów e-mail za pomocą wyrażeń regularnych.
  - Ograniczenie maksymalnej długości pól tekstowych i eliminacja niepożądanych znaków.

#### Korzyści:
- Zmniejszenie ryzyka wprowadzenia nieprawidłowych danych.
- Ograniczenie podatności aplikacji na ataki typu SQL Injection i inne błędy związane z nieprawidłowymi danymi wejściowymi.

---

### 2. Bezpieczeństwo haseł użytkowników

#### Problem:
Hasła są przechowywane w bazie danych w postaci jawnego tekstu, co stanowi poważne zagrożenie w przypadku naruszenia bazy danych.

#### Sugestie:
- Zasugerować haszowanie haseł za pomocą takich algorytmów jak `bcrypt`, `argon2` lub `PBKDF2`.
- Dodanie `salt` do haseł, aby każde miało unikalną wartość nawet w przypadku identycznych danych wejściowych.
- Rozważenie wymuszania silnych haseł, np. wymogu użycia liter, cyfr i znaków specjalnych.

#### Korzyści:
- Ochrona danych użytkowników nawet w przypadku naruszenia bazy danych.
- Zmniejszenie ryzyka przejęcia kont przez osoby niepowołane.

---

### 3. Zewnętrzne serwisy wykorzystywane przez aplikację

#### Problem:
Aplikacja łączy się z bazą danych MySQL lokalnie, bez szyfrowania połączeń, co zwiększa ryzyko przechwycenia danych.

#### Sugestie:
- Sugeruje się włączenie szyfrowania TLS/SSL dla połączeń między aplikacją a bazą danych, szczególnie w środowisku produkcyjnym.

#### Korzyści:
- Zabezpieczenie danych przesyłanych między aplikacją a bazą danych.
- Ograniczenie ryzyka ataków typu Man-in-the-Middle.

---

### 4. Autoryzacja oraz uprawnienia użytkowników

#### Problem:
Brak dokładnego mechanizmu weryfikacji i autoryzacji użytkowników.

#### Sugestie:
- Rozważenie wdrożenia systemu ról (np. Admin, Cashier) z kontrolą dostępu na podstawie przypisanej roli.
- Warto zastosować rozwiązania takie jak JWT (JSON Web Tokens) do przechowywania tokenów sesyjnych.
- Można również wprowadzić wieloskładnikową autentykację (MFA) dla krytycznych operacji.

#### Korzyści:
- Zwiększenie kontroli dostępu do zasobów aplikacji.
- Wyższy poziom bezpieczeństwa danych użytkowników i transakcji.

---

### 5. SQL Injection

#### Problem:
Aplikacja wykorzystuje dynamiczne zapytania SQL, w których dane użytkownika są wstawiane bezpośrednio do zapytań, co naraża system na ataki typu SQL Injection.

Problematyczny fragment:
```java
String query = "Insert INTO item Values('"+name+"','"+email+"','"+mobile+"','"+id+"','"+pwd+"')";
```

#### Sugestie:
- Warto użyć przygotowanych zapytań (Prepared Statements), np.:
  ```java
  String query = "INSERT INTO item (name, email, mobile, id, pass) VALUES (?, ?, ?, ?, ?)";
  PreparedStatement stmt = conn.prepareStatement(query);
  stmt.setString(1, name);
  stmt.setString(2, email);
  stmt.setLong(3, mobile);
  stmt.setString(4, id);
  stmt.setString(5, pwd);
  stmt.executeUpdate();
  ```

#### Korzyści:
- Ograniczenie ryzyka manipulacji danymi przez nieautoryzowane osoby.
- Wyższy poziom bezpieczeństwa wrażliwych danych.

---

### 6. Twardo zakodowane dane logowania

#### Problem:
Dane logowania do bazy danych (np. nazwa użytkownika i hasło) są zakodowane w plikach źródłowych aplikacji.

#### Sugestie:
- Sugeruje się przechowywanie poufnych danych w plikach konfiguracyjnych lub zmiennych środowiskowych (np. `.env`).
- Można użyć bibliotek takich jak `dotenv` lub `System.getenv()` do odczytu danych konfiguracyjnych.
- Zaleca się także wymuszenie użycia TLS/SSL dla wszystkich połączeń z bazą danych.

#### Korzyści:
- Zmniejszenie ryzyka wycieku wrażliwych danych.
- Łatwiejsza zmiana konfiguracji bez modyfikacji kodu źródłowego.

---

### 7. Brak obsługi wyjątków

#### Problem:
Aplikacja wyświetla szczegółowe błędy użytkownikom, co zwiększa ryzyko ujawnienia wrażliwych informacji.

Problematyczny fragment:
```java
JOptionPane.showMessageDialog(null, e);
```

#### Sugestie:
- Można rozważyć użycie bibliotek takich jak `log4j` lub `SLF4J` do rejestrowania błędów w logach serwera.
- Warto prezentować użytkownikom ogólne komunikaty, np. "Wystąpił błąd. Spróbuj ponownie."

#### Korzyści:
- Zmniejszenie ryzyka ujawnienia szczegółowych informacji o systemie.
- Lepsza diagnostyka błędów przez administratorów.

---

### 8. Brak ochrony przed atakami Brute Force

#### Problem:
Aplikacja nie ogranicza liczby nieudanych prób logowania, co czyni ją podatną na ataki siłowe (Brute Force).

#### Sugestie:
- Można rozważyć wprowadzenie limitu prób logowania, np. blokowania konta po określonej liczbie nieudanych prób.
- Sugeruje się dodanie mechanizmów CAPTCHA przy wielu nieudanych próbach logowania.
- Warto wprowadzić mechanizmy opóźniające odpowiedź serwera po każdej nieudanej próbie logowania.

#### Korzyści:
- Ochrona kont użytkowników przed nieautoryzowanym dostępem.
- Redukcja ryzyka wykorzystania automatycznych narzędzi do łamania haseł.

---

### 9. Brak audytu i monitoringu działań w aplikacji

#### Problem:
Aplikacja nie posiada funkcji umożliwiającej śledzenie działań użytkowników, co utrudnia analizę potencjalnych naruszeń bezpieczeństwa.

#### Sugestie:
- Warto rozważyć logowanie krytycznych operacji, takich jak logowanie, zmiana danych czy generowanie raportów.
- Sugeruje się wprowadzenie identyfikatorów użytkowników do logów, aby móc lepiej śledzić ich działania.
- Można także zastosować narzędzia do monitorowania w czasie rzeczywistym w celu wykrywania podejrzanej aktywności.

#### Korzyści:
- Możliwość szybkiego reagowania na incydenty bezpieczeństwa.
- Lepsza kontrola nad działaniami użytkowników w aplikacji.

---

## Podsumowanie

### Wykryte podatności:
1. Brak walidacji danych wejściowych.
2. Przechowywanie haseł w postaci jawnego tekstu.
3. Dynamiczne zapytania SQL podatne na SQL Injection.
4. Brak szyfrowania połączeń z bazą danych.
5. Twardo zakodowane dane logowania.
6. Brak obsługi wyjątków i wyświetlanie szczegółowych błędów.
7. Brak ochrony przed atakami Brute Force.
8. Brak audytu i monitoringu działań w aplikacji.

### Rekomendowane zmiany:
1. Wprowadzenie walidacji danych wejściowych.
2. Zastosowanie haszowania haseł użytkowników.
3. Korzystanie z przygotowanych zapytań SQL (Prepared Statements).
4. Szyfrowanie połączeń z bazą danych za pomocą TLS/SSL.
5. Przechowywanie danych logowania w zmiennych środowiskowych.
6. Poprawna obsługa wyjątków i logowanie błędów.
7. Implementacja mechanizmów ograniczających ataki Brute Force.
8. Wdrożenie monitoringu i audytu działań użytkowników.

Wprowadzenie tych zmian znacząco zwiększy bezpieczeństwo aplikacji oraz ograniczy ryzyko nieautoryzowanego dostępu do danych.

