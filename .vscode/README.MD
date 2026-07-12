# Instalacja skilla z pliku ZIP w VS Code

Ten plik opisuje, jak zainstalowac skill z archiwum:

- `D:\eu-pl-law-tracker\.vscode\eu-pl-law-tracker.zip`

## Opcja 1 (najprostsza): instalacja lokalna przez rozpakowanie

1. Zamknij VS Code (zalecane).
2. Rozpakuj archiwum `eu-pl-law-tracker.zip` do folderu:
   - `C:\Users\grzeg\AppData\Roaming\Code\User\prompts\skills\eu-pl-law-tracker`
3. Po rozpakowaniu sprawdz, czy istnieje plik:
   - `C:\Users\grzeg\AppData\Roaming\Code\User\prompts\skills\eu-pl-law-tracker\SKILL.md`
4. Uruchom ponownie VS Code.
5. Otworz Copilot Chat i rozpocznij nowa rozmowe - skill powinien byc dostepny automatycznie.

## Opcja 2: instalacja z poziomu GUI (jesli masz taka komende)

W niektorych wersjach rozszerzen Copilot moze byc dostepna komenda typu:

- `Chat: Install Skill from ZIP` lub podobna

Kroki:

1. Otworz Command Palette (`Ctrl+Shift+P`).
2. Wyszukaj komende instalacji skilla z ZIP.
3. Wskaz plik:
   - `D:\eu-pl-law-tracker\.vscode\eu-pl-law-tracker.zip`
4. Zrestartuj okno VS Code (`Developer: Reload Window`).

## Weryfikacja

Po instalacji uruchom nowy czat i sprawdz, czy agent/skill zwiazany z `eu-pl-law-tracker` jest widoczny lub wywolywany w odpowiednim kontekście.

## Uwagi

- Jesli skill sie nie laduje, sprawdz strukture folderu po rozpakowaniu. Najczestszy problem to dodatkowy poziom katalogu (np. `...\eu-pl-law-tracker\eu-pl-law-tracker\SKILL.md`).
- Plik `SKILL.md` powinien byc na poziomie glownego katalogu skilla.
