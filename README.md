# Prompts

Ready-to-paste prompts for weak local LLMs. Copy the block between `=== PROMPT START ===` and `=== PROMPT END ===`, then append one production class.

Prefer the English prompt when the model struggles with Polish.

## Contents

| File | Language | Purpose |
|---|---|---|
| [docs/spring-boot-4-test-prompt.en.md](docs/spring-boot-4-test-prompt.en.md) | English | Spring Boot 4 unit + slice test prompt |
| [docs/spring-boot-4-test-prompt.md](docs/spring-boot-4-test-prompt.md) | Polish | Same rules, Polish instructions |

Each file has two parts:

1. A cheatsheet for you (what to focus on).
2. A literal prompt for the model, with four few-shot examples.

## Spring Boot 4 tests — scope

In scope:

- **UNIT_SERVICE** — Mockito, no Spring context (`@ExtendWith(MockitoExtension.class)`)
- **SLICE_WEB** — `@WebMvcTest` + `@MockitoBean` + MockMvc
- **SLICE_JPA** — `@DataJpaTest` + `TestEntityManager`
- **SLICE_JSON** — `@JsonTest` + `JacksonTester`

Out of scope: full `@SpringBootTest`, Testcontainers, E2E.

Boot 4 hard rules baked into the prompt:

- `@MockitoBean` / `@MockitoSpyBean` — never `@MockBean` / `@SpyBean`
- Boot 4 packages (`org.springframework.boot.webmvc.test...`, `org.springframework.boot.data.jpa.test...`)
- JUnit Jupiter + AssertJ + Mockito
- method names: `should{Outcome}When{Condition}`

## How to use

1. Open the English or Polish file.
2. Copy `=== PROMPT START ===` … `=== PROMPT END ===` into the local model as the system / first message.
3. Append **one class and one kind** per request:

```
KIND: UNIT_SERVICE
PACKAGE: com.example.orders
CLASS UNDER TEST:
<paste the class>

DEPENDENCIES:
<paste interfaces, records, exceptions>
```

Do not dump a whole module into one request. Weak models invent types when dependencies are missing.

---

# Prompty

Gotowe prompty do wklejenia w słabe, lokalne modele. Skopiuj blok między `=== PROMPT START ===` a `=== PROMPT END ===`, potem doklej jedną klasę produkcyjną.

Gdy model gorzej radzi sobie z polskim, użyj wersji angielskiej.

## Zawartość

| Plik | Język | Cel |
|---|---|---|
| [docs/spring-boot-4-test-prompt.en.md](docs/spring-boot-4-test-prompt.en.md) | angielski | Prompt testów jednostkowych i slice, Spring Boot 4 |
| [docs/spring-boot-4-test-prompt.md](docs/spring-boot-4-test-prompt.md) | polski | Te same zasady, instrukcje po polsku |

Każdy plik ma dwie części:

1. Ściągawkę dla Ciebie (na czym się skupiać).
2. Dosłowny prompt dla modelu, z czterema przykładami few-shot.

## Testy Spring Boot 4 — zakres

W zakresie:

- **UNIT_SERVICE** — Mockito, bez kontekstu Springa (`@ExtendWith(MockitoExtension.class)`)
- **SLICE_WEB** — `@WebMvcTest` + `@MockitoBean` + MockMvc
- **SLICE_JPA** — `@DataJpaTest` + `TestEntityManager`
- **SLICE_JSON** — `@JsonTest` + `JacksonTester`

Poza zakresem: pełny `@SpringBootTest`, Testcontainers, E2E.

Twarde zasady Boot 4 wplecione w prompt:

- `@MockitoBean` / `@MockitoSpyBean` — nigdy `@MockBean` / `@SpyBean`
- pakiety Boot 4 (`org.springframework.boot.webmvc.test...`, `org.springframework.boot.data.jpa.test...`)
- JUnit Jupiter + AssertJ + Mockito
- nazwy metod: `should{Outcome}When{Condition}`

## Jak używać

1. Otwórz plik angielski albo polski.
2. Skopiuj `=== PROMPT START ===` … `=== PROMPT END ===` do lokalnego modelu jako system / pierwsze przesłanie.
3. Doklej **jedną klasę i jeden typ** na request:

```
TYP: UNIT_SERVICE
PAKIET: com.example.orders
KLASA POD TESTEM:
<wklej klasę>

ZALEŻNOŚCI:
<wklej interfejsy, rekordy, wyjątki>
```

Nie wrzucaj całego modułu w jeden request. Słaby model zgaduje typy, gdy brakuje zależności.
