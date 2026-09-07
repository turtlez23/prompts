# Spring Boot 4 — unit and slice tests

Cheatsheet for you, not for the model. The text the model must see lives in `prompt.md`. How to attach it in IntelliJ + ProxyAI lives in `usage.md`.

# Part 1. What to test

## Pyramid

Write many fast service tests, fewer HTTP/JPA/JSON slice tests, and zero full application context.

| Layer | Tool | Assert | Leave alone |
|---|---|---|---|
| Service / domain logic | `@ExtendWith(MockitoExtension.class)` | business rules, exceptions, mapping, collaborator writes | Spring DI, HTTP, database |
| Controller | `@WebMvcTest` + `@MockitoBean` | status, JSON, URL, Bean Validation | business rules |
| Repository | `@DataJpaTest` + `TestEntityManager` | custom `@Query`, join/sort, constraints, relations | stock `findById` / `save` |
| DTO / JSON | `@JsonTest` + `JacksonTester` | field names, ignore, dates, enums | service logic |

**Selection rule:** if the test needs a Spring context, it is not a unit test. Call it a slice test and load the *narrowest* slice.

## Service — most of the value lives here

Test **public API behavior**, not private methods and not Mockito itself.

Cover:

- branches (`if`, `switch`, early return)
- DTO ↔ entity mapping (fields the business actually sets)
- exceptions: not found, duplicate, illegal state, empty input
- side effects that **are requirements**: `save`, event publish, HTTP client call
- boundaries: `null`, blank, `0`, empty list, already-existing row

Do not test:

- getters / setters / Lombok
- that Spring injects a bean
- that Mockito returns the stub you configured, with no assertion on the SUT
- implementation details (`verify` call order unless order *is* the contract)

## Controller — HTTP contract

Keep controllers thin. A slice test checks the door, not the warehouse.

Cover:

- routing (`GET/POST/...`, path variable, query)
- statuses (`200`, `201`, `400`, `404`)
- JSON shape (keys the client sees)
- Bean Validation (`@NotBlank`, `@Valid`) → `400`
- that the service **was called with the expected argument**

Do not put “is this email unique?” here — that belongs in the service test.  
On Boot 4 the service mock is **`@MockitoBean`**, never `@MockBean`.

## Repository — only what JPA does not guarantee

Cover:

- `@Query` (JPQL/native), `JOIN FETCH`, sort, pagination
- derived queries that join several fields / relations
- uniqueness, `nullable`, cascade — if that is your contract

Do not test `repository.save(entity)` or `findById` — Spring Data already does.

Fixture: persist with `TestEntityManager.persistAndFlush(...)`, then call the **repository method under test**. Do not seed data with the same method you are testing.

## JSON — API shape

Cover:

- JSON names (`@JsonProperty`, snake_case)
- hidden fields (`@JsonIgnore`, passwords)
- date format (`Instant` / `LocalDate`)
- enums (name vs code)

## Spring Boot 4 — hard breaks vs 3.x

Weak models copy old tutorials. Force Boot 4:

| Instead of (3.x / blog) | Use (4.x) |
|---|---|
| `@MockBean` | `@MockitoBean` (`org.springframework.test.context.bean.override.mockito`) |
| `@SpyBean` | `@MockitoSpyBean` |
| `org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest` | `org.springframework.boot.webmvc.test.autoconfigure.WebMvcTest` |
| `org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest` | `org.springframework.boot.data.jpa.test.autoconfigure.DataJpaTest` |
| old `TestEntityManager` | `org.springframework.boot.jpa.test.autoconfigure.TestEntityManager` |
| `spring-boot-starter-test` alone for slices | add the slice starter below |
| JUnit 4 (`org.junit.Test`, `@RunWith`) | JUnit 5/6 (`org.junit.jupiter.api.Test`) |

Test dependencies (Maven, `scope=test`):

- always: `spring-boot-starter-test`
- MVC: `spring-boot-starter-webmvc-test`
- Data JPA: `spring-boot-starter-data-jpa-test`
- JSON: `@JsonTest` lives in `spring-boot-test-autoconfigure` (pulled in by starter-test)
- JPA slice database: H2 (`com.h2database:h2`)

## Test hygiene

- Name: `shouldRejectCreateWhenEmailAlreadyExists`, not `testCreate2`.
- One scenario = one `@Test`. Input table → `@ParameterizedTest`.
- Layout: Arrange / Act / Assert (`given` / `when` / `then`).
- AssertJ: `assertThat(...)`, `assertThatThrownBy(...)`.
- `verify()` only when the interaction **is** a requirement (`save`, event). Do not verify every `findById`.
- Never: `@SpringBootTest`, `Thread.sleep`, a production database, mocking the class under test.
