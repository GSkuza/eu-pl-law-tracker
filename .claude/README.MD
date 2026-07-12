# Instalacja skilla `eu-pl-law-tracker`

Skill jest spakowany jako `eu-pl-law-tracker.zip` i zawiera plik `SKILL.md` w katalogu głównym oraz foldery `references/` i `scripts/`. Poniżej instrukcje instalacji na trzech platformach.

---

## a) Claude.ai (web / desktop app – zwykły czat)

1. Upewnij się, że masz włączone **Code execution and file creation** – Ustawienia → Capabilities (skille tego wymagają).
   ⚠️ Sam toggle nie wystarczy dla tego skilla – zobacz **[Domeny sieciowe (allowed domains)](#domeny-sieciowe-wymagane-przez-skill-allowed-domains)** niżej, inaczej pobieranie z EUR-Lex/ISAP/RCL się nie uda.
2. Przejdź do **Customize → Skills** (lewy panel boczny).
3. Kliknij **„+”**, następnie **„Create skill”** (lub **„Upload a skill”**, w zależności od wersji UI).
4. Wskaż plik `eu-pl-law-tracker.zip` (nie musisz go rozpakowywać – Claude sam odczyta `SKILL.md` w środku).
5. Po wgraniu skill pojawi się na liście – **włącz przełącznik** (toggle), inaczej Claude go zignoruje.
6. Test: napisz np. „sprawdź status PPWR w Polsce” – w „thinking” powinno pojawić się odwołanie do skilla.

Uwaga: skill wgrany w ten sposób jest **prywatny dla Twojego konta**. Jeśli chcesz udostępnić go całej organizacji (plan Team/Enterprise), zrób to przez **Organization settings → Skills → „+ Add”** (provisioning) albo funkcję „Share” przy skillu.

---

## b) Claude Code

Dwie opcje – wybierz jedną.

### Opcja 1: instalacja plikowa (najprostsza dla gotowego ZIP-a)

```bash
# instalacja osobista – dostępna we wszystkich projektach
mkdir -p ~/.claude/skills
unzip eu-pl-law-tracker.zip -d ~/.claude/skills/eu-pl-law-tracker

# ALBO instalacja per-projekt – trafia do repo, współdzielona przez git
mkdir -p .claude/skills
unzip eu-pl-law-tracker.zip -d .claude/skills/eu-pl-law-tracker
```

Upewnij się, że po rozpakowaniu `SKILL.md` leży bezpośrednio w `~/.claude/skills/eu-pl-law-tracker/SKILL.md` (bez dodatkowego zagnieżdżonego folderu).

Uruchom ponownie sesję Claude Code (lub nową sesję) i sprawdź komendą:

```
/skills
```

Skill powinien być widoczny na liście i będzie automatycznie ładowany, gdy kontekst rozmowy pasuje do jego opisu.

### Opcja 2: przez SDK z jawną konfiguracją

Jeśli używasz Claude Code przez Agent SDK, upewnij się że `settingSources`/`setting_sources` zawiera `"project"` lub `"user"` (w zależności gdzie umieściłeś folder) oraz że `"Skill"` jest dodane do `allowed_tools`.

⚠️ To sprawia, że skill się *załaduje* – ale żeby faktycznie **pobierał dane** z EUR-Lex/ISAP/RCL, musisz też skonfigurować sieć – zobacz sekcję **[Domeny sieciowe (allowed domains)](#domeny-sieciowe-wymagane-przez-skill-allowed-domains)** niżej.

---

## Domeny sieciowe wymagane przez skill (allowed domains)

Skill nie pracuje z pamięci – zgodnie z jego zasadą nr 1 (`SKILL.md`), każdy CELEX, ELI, numer druku czy data musi pochodzić z realnie pobranego źródła. To oznacza wychodzące połączenia do konkretnych domen. Jeśli środowisko Claude ich nie zezwala, skill *triggeruje się* normalnie (to zależy tylko od pola `description`), ale krok pobierania danych zwróci błąd sieciowy albo – gorzej – zostanie po cichu pominięty i raport wyjdzie niepełny.

Pełna lista, na podstawie `references/source-map.md` i `references/polish-law-sources.md`:

| Domena | Źródło | Krytyczność |
|---|---|---|
| `eur-lex.europa.eu` | EUR-Lex – CELEX, Dziennik Urzędowy UE, Procedure | wymagana zawsze |
| `data.europa.eu` | ELI (identyfikatory aktów UE) | wymagana zawsze |
| `isap.sejm.gov.pl` | ISAP – status aktu w Polsce (obowiązujący/uchylony/zmieniany) | wymagana zawsze |
| `dziennikustaw.gov.pl` | Dziennik Ustaw RP – data ogłoszenia i wejścia w życie | wymagana dla kroku „status w Polsce” |
| `legislacja.rcl.gov.pl` | RCL – etap projektu rządowego | wymagana dla kroku „status w Polsce” |
| `www.sejm.gov.pl` | druki sejmowe, etapy prac legislacyjnych | wymagana dla kroku „status w Polsce” |
| `www.gov.pl` | materiały urzędowe (traktowane jako niewiążące) | pomocnicza |

### Claude.ai / Cowork

Ustawienie: **Settings → Capabilities → Code execution and file creation → Allow network egress** (dla organizacji: **Organization settings → Capabilities**).

„Allow network egress” ma trzy tryby **Domain allowlist**:

- **None** – brak internetu. Skill się nie napełni danymi.
- **Package managers only** – domyślne dla planu Team; tylko npm/PyPI/GitHub itp. – też za mało dla źródeł prawnych.
- **All domains** – pełny dostęp. Najprostsze dla tego skilla, bo wszystkie źródła (EUR-Lex, ISAP, RCL, Sejm, gov.pl) są publiczne i wyłącznie do odczytu, więc ryzyko wycieku danych jest tu głównie teoretyczne – ale oceń to według własnej polityki bezpieczeństwa.

Jeśli wolisz zostać przy **Package managers only**, dodaj każdą domenę z tabeli powyżej osobno w polu **Additional allowed domains**. Na planie Team/Enterprise ta opcja bywa zablokowana na poziomie organizacji – wtedy o zmianę poproś administratora (**Organization settings → Capabilities**).

### Claude Code

Tu działają **dwa niezależne mechanizmy** i przy pracy z tym skillem warto ustawić oba:

**1. Permissions** (obowiązuje zawsze, także bez sandboxa) – reguły `WebFetch(domain:...)` w `settings.json` (`~/.claude/settings.json` dla wszystkich projektów albo `.claude/settings.json` w repo, współdzielone przez git):

```json
{
  "permissions": {
    "allow": [
      "WebFetch(domain:eur-lex.europa.eu)",
      "WebFetch(domain:data.europa.eu)",
      "WebFetch(domain:isap.sejm.gov.pl)",
      "WebFetch(domain:dziennikustaw.gov.pl)",
      "WebFetch(domain:legislacja.rcl.gov.pl)",
      "WebFetch(domain:www.sejm.gov.pl)",
      "WebFetch(domain:www.gov.pl)"
    ]
  }
}
```

Bez tych wpisów Claude Code i tak zapyta o zgodę na każdą domenę przy pierwszym użyciu – możesz wtedy wybrać „always allow”, co dopisze regułę do `settings.json` automatycznie.

**2. Sandbox** (jeśli używasz `/sandbox`) – to osobna warstwa, egzekwowana na poziomie systemu operacyjnego (Seatbelt/bubblewrap) tylko dla poleceń Bash i ich procesów potomnych, np. gdy skrypty z `scripts/` (`eu_law_parse.py`, `legal_date_extractor.py` itd.) same odpalają zapytania HTTP. Wpis w `permissions.allow` go **nie zastępuje** – trzeba dodać domeny osobno:

```json
{
  "sandbox": {
    "enabled": true,
    "network": {
      "allowedDomains": [
        "eur-lex.europa.eu",
        "data.europa.eu",
        "isap.sejm.gov.pl",
        "dziennikustaw.gov.pl",
        "legislacja.rcl.gov.pl",
        "www.sejm.gov.pl",
        "www.gov.pl"
      ]
    }
  }
}
```

W środowisku enterprise z `managed-settings.json` i `allowManagedDomainsOnly: true` liczy się tylko lista z pliku managed – wtedy o dopisanie tych domen musi poprosić administrator.

---

## c) Cowork

Cowork korzysta z tej samej listy skilli co Claude.ai (**Customize → Skills**) w ramach Twojego konta – jeśli włączysz skill w Chat, jest on też dostępny w Cowork, i odwrotnie.

1. W Cowork kliknij **Customize** w lewym pasku bocznym.
2. Kliknij **„+”**, przejdź do zakładki **Skills**, wybierz **„Upload a skill”**.
3. Wgraj `eu-pl-law-tracker.zip`.
4. Włącz skill (toggle) – jeśli był już włączony w Chat, nie musisz nic dodatkowo robić.
5. W sesji Cowork wpisz `/` żeby zobaczyć listę zainstalowanych skilli i wybrać go ręcznie, albo po prostu opisz zadanie naturalnie (np. „sprawdź obowiązki compliance dla EUDR”) – Claude sam rozpozna kiedy użyć skilla.

⚠️ Cowork dziedziczy ustawienie **Allow network egress** z Twojego konta/organizacji – zobacz **[Domeny sieciowe (allowed domains)](#domeny-sieciowe-wymagane-przez-skill-allowed-domains)** wyżej, bo domyślny tryb „Package managers only” (typowy dla planu Team) zablokuje pobieranie z EUR-Lex/ISAP.

---

## Uwagi wspólne

- Skille mogą uruchamiać kod i instalować pakiety (Python/npm) – instaluj tylko skille, którym ufasz. Ten skill jest Twojego autorstwa, więc jest bezpieczny.
- Jeśli po instalacji skill się nie triggeruje, sprawdź czy fraza w Twoim pytaniu pasuje do pola `description` w `SKILL.md` (np. „PPWR”, „CBAM”, „status prawny”, „compliance UE”).
- Po każdej zmianie w `SKILL.md` lub plikach `references/` w Claude.ai/Cowork trzeba wgrać skill ponownie (nowa wersja ZIP); w Claude Code wystarczy nadpisać pliki na dysku i zrestartować sesję.
- Skill sam w sobie nie zawiera żadnych kluczy API ani sekretów – cała „krytyczność” bezpieczeństwa sprowadza się do tego, jakie domeny ma zgodę odpytywać (patrz **[Domeny sieciowe (allowed domains)](#domeny-sieciowe-wymagane-przez-skill-allowed-domains)**). Jeśli po instalacji skill triggeruje się poprawnie, ale raporty mają puste sekcje „Status w Polsce” albo błędy pobierania – to niemal zawsze jest to, a nie problem z samym skillem.