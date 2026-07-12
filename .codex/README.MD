# Instalacja skilla EU/PL Law Tracker w Codex

Archiwum `eu-pl-law-tracker.zip` zawiera skill do wyszukiwania i analizy aktów
prawnych UE oraz powiązanych polskich aktów i projektów.

## Instalacja w PowerShell

1. Zamknij Codex, jeśli jest uruchomiony.
2. Otwórz PowerShell.
3. Sprawdź, czy docelowy katalog nie zawiera już tego skilla:

   ```powershell
   Test-Path "$HOME\.codex\skills\eu-pl-law-tracker"
   ```

   Jeżeli polecenie zwróci `True`, przenieś istniejący katalog w inne miejsce albo
   usuń go, jeśli nie jest już potrzebny.

4. Utwórz katalog docelowy i rozpakuj archiwum:

   ```powershell
   $destination = "$HOME\.codex\skills\eu-pl-law-tracker"
   New-Item -ItemType Directory -Path $destination -Force | Out-Null
   Expand-Archive -LiteralPath "D:\eu-pl-law-tracker\.codex\eu-pl-law-tracker.zip" -DestinationPath $destination
   ```

5. Sprawdź, czy plik definicji skilla znajduje się we właściwym miejscu:

   ```powershell
   Test-Path "$HOME\.codex\skills\eu-pl-law-tracker\SKILL.md"
   ```

   Wynik powinien mieć wartość `True`.

6. Uruchom ponownie Codex. Skill będzie dostępny od następnej sesji lub tury.

## Użycie

Możesz wywołać skill po nazwie, na przykład:

```text
$eu-pl-law-tracker sprawdź aktualny status PPWR w UE i w Polsce
```

Codex może również dobrać ten skill automatycznie, gdy pytanie dotyczy statusu,
terminów stosowania lub polskiego wdrożenia regulacji UE.
