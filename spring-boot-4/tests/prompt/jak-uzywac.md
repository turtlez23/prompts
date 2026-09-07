# Jak używać tego promptu

Trzy pliki w tym katalogu:

| Plik | Kto czyta | Podawać modelowi? |
|---|---|---|
| `opis.md` | ty | nie |
| `prompt.md` | model | **tak** |
| `jak-uzywac.md` | ty | nie |

Model ma dostać **surowy markdown** z `prompt.md`. Nie wklejaj podglądu z GitHuba (HTML). W IntelliJ zwykle w ogóle nie kopiujesz tekstu — podpinasz plik.

## IntelliJ + ProxyAI (zalecane)

ProxyAI `@Files` widzi tylko pliki **bieżącego projektu IntelliJ**. Sklonowane repo `prompts` obok aplikacji nie wystarczy, dopóki nie dodasz go do projektu.

### 1. Włóż `prompt.md` do projektu w IDE

Wybierz jeden sposób:

- Skopiuj ten katalog do repo Spring Boot, np. `prompts/spring-boot-4-tests/`.
- Albo w IntelliJ: **File → Project Structure → Modules → + → Import Module** (albo drugi content root wskazujący na repo `prompts`).

W projekcie wystarczy `prompt.md`. `opis.md` i `jak-uzywac.md` możesz pominąć, jeśli chcesz mniejszy drzewo.

### 2. Czat: podepnij prompt + klasę pod testem

1. Otwórz okno czatu ProxyAI.
2. Wpisz `@` → **Files** → wybierz `prompt.md` z tego katalogu.  
   **Nie** podpinaj `opis.md`, `jak-uzywac.md` ani całego folderu — dodatkowy tekst gubi słaby model.
3. Znowu `@` → **Files** → wybierz klasę Javy do przetestowania.
4. Tak samo podepnij collaboratorów (interfejs repozytorium, DTO, wyjątki). Brak typów = model je wymyśli.
5. Wyślij krótką wiadomość, jedna klasa, jeden typ:

```
TYP: UNIT_SERVICE
PAKIET: com.example.orders
```

Typy: `UNIT_SERVICE` | `SLICE_WEB` | `SLICE_JPA` | `SLICE_JSON`.

Gdy nie podasz `TYP`, prompt zgadnie po klasie (serwis / kontroler / repo / DTO).

### 3. Opcjonalnie: persona w ProxyAI

Jeśli testy generujesz często i nie chcesz za każdym razem `@Files prompt.md`:

1. Otwórz `prompt.md`.
2. Skopiuj **surowy plik** (edytor, nie podgląd w przeglądarce).
3. **Settings / Preferences → Tools → ProxyAI → Prompts**.
4. Dodaj personę, np. `Spring Boot 4 tests`.
5. Wklej zawartość pliku w instrukcje persony.
6. Na czacie `@` → **Personas** → ta persona, potem `@` klasę Javy.

Persony w ProxyAI są globalne dla IDE, dopóki ich nie wkleisz ponownie. Trzymaj `prompt.md` w gicie jako źródło prawdy.

## Gdy nie używasz ProxyAI

1. Otwórz `prompt.md` jako plik (Raw na GitHubie albo edytor).
2. Skopiuj cały plik.
3. Wklej jako system / pierwsze przesłanie.
4. Doklej jedną klasę:

```
TYP: UNIT_SERVICE
PAKIET: com.example.orders
KLASA POD TESTEM:
<wklej klasę>

ZALEŻNOŚCI:
<wklej interfejsy, rekordy, wyjątki>
```

## Zasady, które trzymają słaby model w ryzach

- Jeden request = jedna klasa + jeden `TYP`.
- Nigdy nie podpinaj całego tego folderu.
- Jeśli model gorzej radzi sobie z polskim, użyj katalogu `prompt.en/`.
