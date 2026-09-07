# Prompts

Ready-to-paste prompts for weak local LLMs. Each prompt set is a folder: description (for you), `prompt.md` (for the model), and usage notes.

In IntelliJ + ProxyAI attach **only** `prompt.md` plus the Java class. See the usage file in each set.

Prefer the English set when the model struggles with Polish.

## Layout

One directory per topic, one subdirectory per prompt set:

```
spring-boot-4/
  tests/
    prompt.en/     ← English: description.md, prompt.md, usage.md
    prompt/        ← Polish: opis.md, prompt.md, jak-uzywac.md
```

Add more sets later as sibling folders (`spring-boot-4/foo/`, `quarkus/tests/`, …).

## Contents

| Path | Language | Attach to the model |
|---|---|---|
| [spring-boot-4/tests/prompt.en/prompt.md](spring-boot-4/tests/prompt.en/prompt.md) | English | yes |
| [spring-boot-4/tests/prompt.en/description.md](spring-boot-4/tests/prompt.en/description.md) | English | no |
| [spring-boot-4/tests/prompt.en/usage.md](spring-boot-4/tests/prompt.en/usage.md) | English | no |
| [spring-boot-4/tests/prompt/prompt.md](spring-boot-4/tests/prompt/prompt.md) | Polish | yes |
| [spring-boot-4/tests/prompt/opis.md](spring-boot-4/tests/prompt/opis.md) | Polish | no |
| [spring-boot-4/tests/prompt/jak-uzywac.md](spring-boot-4/tests/prompt/jak-uzywac.md) | Polish | no |

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

## How to use (short)

Full IntelliJ + ProxyAI steps: [spring-boot-4/tests/prompt.en/usage.md](spring-boot-4/tests/prompt.en/usage.md).

1. Put `prompt.md` inside the IntelliJ project (copy the folder, or add this repo as a module).
2. In ProxyAI chat: `@Files` → `prompt.md`, then `@Files` → the Java class (and its DTOs / exceptions).
3. Send `KIND: UNIT_SERVICE` (or `SLICE_WEB` / `SLICE_JPA` / `SLICE_JSON`).

Do not dump a whole module into one request. Weak models invent types when dependencies are missing.

---

# Prompty

Gotowe prompty do słabych, lokalnych modeli. Każdy zestaw to katalog: opis (dla Ciebie), `prompt.md` (dla modelu) i instrukcja użycia.

W IntelliJ + ProxyAI podpinaj **tylko** `prompt.md` oraz klasę Javy. Szczegóły są w pliku użycia w zestawie.

Gdy model gorzej radzi sobie z polskim, użyj zestawu angielskiego.

## Układ

Jeden katalog na temat, jeden podkatalog na zestaw promptów:

```
spring-boot-4/
  tests/
    prompt.en/     ← angielski: description.md, prompt.md, usage.md
    prompt/        ← polski: opis.md, prompt.md, jak-uzywac.md
```

Kolejne zestawy dokładaj obok (`spring-boot-4/foo/`, `quarkus/tests/`, …).

## Zawartość

| Ścieżka | Język | Podawać modelowi |
|---|---|---|
| [spring-boot-4/tests/prompt.en/prompt.md](spring-boot-4/tests/prompt.en/prompt.md) | angielski | tak |
| [spring-boot-4/tests/prompt.en/description.md](spring-boot-4/tests/prompt.en/description.md) | angielski | nie |
| [spring-boot-4/tests/prompt.en/usage.md](spring-boot-4/tests/prompt.en/usage.md) | angielski | nie |
| [spring-boot-4/tests/prompt/prompt.md](spring-boot-4/tests/prompt/prompt.md) | polski | tak |
| [spring-boot-4/tests/prompt/opis.md](spring-boot-4/tests/prompt/opis.md) | polski | nie |
| [spring-boot-4/tests/prompt/jak-uzywac.md](spring-boot-4/tests/prompt/jak-uzywac.md) | polski | nie |

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

## Jak używać (skrót)

Pełne kroki IntelliJ + ProxyAI: [spring-boot-4/tests/prompt/jak-uzywac.md](spring-boot-4/tests/prompt/jak-uzywac.md).

1. Włóż `prompt.md` do projektu IntelliJ (skopiuj katalog albo dodaj to repo jako moduł).
2. Na czacie ProxyAI: `@Files` → `prompt.md`, potem `@Files` → klasa Javy (oraz DTO / wyjątki).
3. Wyślij `TYP: UNIT_SERVICE` (albo `SLICE_WEB` / `SLICE_JPA` / `SLICE_JSON`).

Nie wrzucaj całego modułu w jeden request. Słaby model zgaduje typy, gdy brakuje zależności.
