# Spring Boot 4 — testy jednostkowe i slice

Ściągawka dla Ciebie, nie dla modelu. Tekst, który ma dostać model, jest w `prompt.md`. Jak go podpiąć w IntelliJ + ProxyAI jest w `jak-uzywac.md`.

# Część 1. Na czym się skupiać

## Piramida

Pisz dużo szybkich testów serwisów, mniej testów warstwy HTTP/JPA/JSON, zero pełnego kontekstu aplikacji.

| Warstwa | Narzędzie | Co weryfikujesz | Czego nie ruszasz |
|---|---|---|---|
| Serwis / logika | `@ExtendWith(MockitoExtension.class)` | reguły biznesowe, wyjątki, mapowanie, zapis collaboratorom | Spring DI, HTTP, baza |
| Kontroler | `@WebMvcTest` + `@MockitoBean` | status, JSON, URL, Bean Validation | reguły biznesowe |
| Repozytorium | `@DataJpaTest` + `TestEntityManager` | własne `@Query`, join/sort, constrainty, relacje | `findById` / `save` z pudełka |
| DTO / JSON | `@JsonTest` + `JacksonTester` | nazwy pól, ignore, daty, enumy | logika serwisu |

**Reguła wyboru:** jeśli test wymaga kontekstu Springa, to nie jest unit test — nazwij go slice testem i ładuj *najwęższą* warstwę.

## Serwis — tu leży większość wartości

Testujesz **zachowanie publicznego API klasy**, nie prywatne metody i nie Mockito.

Skup się na:

- gałęziach (`if`, `switch`, early return)
- mapowaniu DTO ↔ encja (pola, które biznes naprawdę ustawia)
- wyjątkach: not found, duplicate, nielegalny stan, puste wejście
- skutkach ubocznych, które **są wymaganiem**: `save`, publikacja eventu, wywołanie klienta HTTP
- granicach: `null`, blank, `0`, pusta lista, już istniejący rekord

Nie testuj:

- getterów / setterów / Lomboka
- tego, że Spring wstrzyknie beana
- tego, że Mockito zwraca to, co sam ustawiłeś, bez asercji na SUT
- implementacji (`verify` kolejności wywołań, chyba że kolejność *jest* kontraktem)

## Kontroler — kontrakt HTTP

Kontroler ma być cienki. Slice test sprawdza drzwi, nie magazyn.

Skup się na:

- routing (`GET/POST/...`, path variable, query)
- statusach (`200`, `201`, `400`, `404`)
- kształcie JSON (klucze, które klient widzi)
- Bean Validation (`@NotBlank`, `@Valid`) → `400`
- tym, że serwis **został wywołany z oczekiwanym argumentem**

Nie wkładaj tu logiki „czy email jest unikalny” — to test serwisu.  
W Boot 4 mock serwisu to **`@MockitoBean`**, nigdy `@MockBean`.

## Repozytorium — tylko to, czego JPA nie gwarantuje

Skup się na:

- `@Query` (JPQL/native), `JOIN FETCH`, sort, paginacja
- derived query, które łączą kilka pól / relacji
- unikalności, `nullable`, cascade — jeśli to Wasz kontrakt

Nie pisz testu na `repository.save(entity)` ani `findById` — to Spring Data.

Fixture: zapisuj dane przez `TestEntityManager.persistAndFlush(...)`, a potem wołaj **metodę repozytorium**. Nie przygotowuj danych tą samą metodą, którą testujesz.

## JSON — kształt API

Skup się na:

- nazwach JSON (`@JsonProperty`, snake_case)
- polach ukrytych (`@JsonIgnore`, hasła)
- formacie dat (`Instant` / `LocalDate`)
- enumach (nazwa vs kod)

## Spring Boot 4 — twarde różnice względem 3.x

Słabe modele uczą się ze starych tutoriali. Wymuś Boot 4:

| Zamiast (3.x / tutorial) | Używaj (4.x) |
|---|---|
| `@MockBean` | `@MockitoBean` (`org.springframework.test.context.bean.override.mockito`) |
| `@SpyBean` | `@MockitoSpyBean` |
| `org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest` | `org.springframework.boot.webmvc.test.autoconfigure.WebMvcTest` |
| `org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest` | `org.springframework.boot.data.jpa.test.autoconfigure.DataJpaTest` |
| stary `TestEntityManager` | `org.springframework.boot.jpa.test.autoconfigure.TestEntityManager` |
| sam `spring-boot-starter-test` na slice | dociągnij starter warstwy (niżej) |
| JUnit 4 (`org.junit.Test`, `@RunWith`) | JUnit 5/6 (`org.junit.jupiter.api.Test`) |

Zależności testowe (Maven, `scope=test`):

- zawsze: `spring-boot-starter-test`
- MVC: `spring-boot-starter-webmvc-test`
- Data JPA: `spring-boot-starter-data-jpa-test`
- JSON: `JsonTest` jest w `spring-boot-test-autoconfigure` (ciągnie go starter-test)
- baza w slice JPA: H2 (`com.h2database:h2`)

## Higiena testu

- Nazwa: `shouldRejectCreateWhenEmailAlreadyExists`, nie `testCreate2`.
- Jeden scenariusz = jeden `@Test`. Tabela wejść → `@ParameterizedTest`.
- Układ: Arrange / Act / Assert (given / when / then).
- AssertJ: `assertThat(...)`, `assertThatThrownBy(...)`.
- `verify()` tylko gdy interakcja **jest** wymaganiem (save, event). Nie weryfikuj każdego `findById`.
- Zero: `@SpringBootTest`, `Thread.sleep`, prawdziwa baza produkcyjna, mock klasy pod testem.
