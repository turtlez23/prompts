# How to use this prompt

Three files in this folder:

| File | Who reads it | Attach to the model? |
|---|---|---|
| `description.md` | you | no |
| `prompt.md` | the model | **yes** |
| `usage.md` | you | no |

Give the model **raw markdown** from `prompt.md`. Do not paste a GitHub preview (rendered HTML). In IntelliJ you usually do not copy-paste at all — you attach the file.

## IntelliJ + ProxyAI (recommended)

ProxyAI `@Files` only sees files that belong to the **current IntelliJ project**. Clone of this prompts repo sitting next to the app is not enough until you add it to the project.

### 1. Put `prompt.md` on the project classpath of the IDE

Pick one:

- Copy this folder into the Spring Boot repo, e.g. `prompts/spring-boot-4-tests/`.
- Or in IntelliJ: **File → Project Structure → Modules → + → Import Module** (or attach the `prompts` repo as a second content root).

You need at least `prompt.md` inside the project. Skip `description.md` and `usage.md` if you want a smaller tree.

### 2. Chat: attach prompt + class under test

1. Open the ProxyAI chat tool window.
2. Type `@` → **Files** → select `prompt.md` from this folder.  
   Do **not** attach `description.md`, `usage.md`, or the whole folder — extra prose confuses a weak model.
3. Type `@` → **Files** again → select the Java class to test.
4. Attach collaborators the same way (repository interface, DTO, exceptions). Missing types make the model invent them.
5. Send a short message, one class, one kind:

```
KIND: UNIT_SERVICE
PACKAGE: com.example.orders
```

Kinds: `UNIT_SERVICE` | `SLICE_WEB` | `SLICE_JPA` | `SLICE_JSON`.

If you omit `KIND`, the prompt infers it from the class (service / controller / repo / DTO).

### 3. Optional: save it as a ProxyAI persona

If you generate tests often and do not want to `@Files prompt.md` every time:

1. Open `prompt.md`.
2. Copy the **raw file** (Editor, not a browser preview).
3. **Settings / Preferences → Tools → ProxyAI → Prompts**.
4. Add a persona, e.g. `Spring Boot 4 tests`.
5. Paste the file contents into the persona instructions.
6. In chat, type `@` → **Personas** → that persona, then `@` the Java class.

Personas in ProxyAI are IDE-global unless you re-paste them. Keep `prompt.md` in git as the source of truth.

## If you are not using ProxyAI

1. Open `prompt.md` as a file (Raw on GitHub, or the editor).
2. Copy the entire file.
3. Paste it as the system / first message.
4. Append one class:

```
KIND: UNIT_SERVICE
PACKAGE: com.example.orders
CLASS UNDER TEST:
<paste the class>

DEPENDENCIES:
<paste interfaces, records, exceptions>
```

## Rules that keep weak models on track

- One request = one class + one `KIND`.
- Never attach this whole folder.
- Prefer the English files (`prompt.en/`) if the model is worse at Polish.
