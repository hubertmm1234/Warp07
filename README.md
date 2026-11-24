# NAZWA_PROJEKTU

[![Build](https://github.com/OWNER/REPOSITORY/actions/workflows/build.yml/badge.svg)](https://github.com/OWNER/REPOSITORY/actions/workflows/build.yml)

Krótki opis projektu: co robi aplikacja i do czego służy.

## Wymagania

- .NET SDK `X.0` (zgodny z wersją użytą w workflow)
- Git
- Opcjonalnie: WSL z Ubuntu (do lokalnego uruchamiania skryptów Bash)

## Uruchomienie lokalne

```powershell
dotnet restore "GithubActions/GithubActions.csproj"
dotnet build "GithubActions/GithubActions.csproj" --configuration Release
dotnet run --project "GithubActions/GithubActions.csproj"
```

## Continuous Integration

Projekt korzysta z GitHub Actions. Definicja workflow znajduje się w pliku
[`.github/workflows/build.yml`](.github/workflows/build.yml).

### Kiedy workflow się uruchamia

| Zdarzenie      | Gałąź docelowa | Opis                                               |
| -------------- | -------------- | -------------------------------------------------- |
| `push`         | `main`         | Kontrola po każdej zmianie wprowadzonej do `main`. |
| `pull_request` | `main`         | Kontrola każdego Pull Requesta kierowanego do `main`. |

Push do brancha roboczego uruchamia kontrolę wtedy, gdy ma on otwarty Pull Request do `main`.

### Co jest sprawdzane

- **Projekt:** `GithubActions/GithubActions.csproj` 
- **Wersja .NET:** `X.0`
- **Środowisko:** `ubuntu-latest`

Job `build` wykonuje kolejno:

1. **Checkout repository** – pobranie kodu.
2. **Setup .NET** – instalacja wskazanej wersji SDK.
3. **Check repository structure** – uruchomienie skryptu `scripts/check-repository.sh`, który sprawdza, czy:
   - podano ścieżkę do projektu,
   - wskazany plik `.csproj` istnieje,
   - plik `README.md` istnieje i nie jest pusty.

   Przy niespełnieniu warunku skrypt wypisuje komunikat `ERROR: ...` i kończy się kodem `1`, co zatrzymuje job.
4. **Restore** – przywrócenie zależności (`dotnet restore`).
5. **Build** – kompilacja w konfiguracji Release (`dotnet build`).
6. **Publish project** – przygotowanie wyniku kompilacji (`dotnet publish` do `./artifacts/app`).
7. **Upload build artifact** – wysłanie opublikowanej aplikacji jako artefaktu.

Skrypt można uruchomić lokalnie (w Bashu, np. WSL/Ubuntu) z katalogu głównego repozytorium:

```bash
bash scripts/check-repository.sh "./src/MyApp/MyApp.csproj"
echo $?   # 0 oznacza sukces
```

### Artefakt kompilacji

| Właściwość           | Wartość                                          |
| -------------------- | ------------------------------------------------ |
| Nazwa                | `app-<SHA commita>`                              |
| Zawartość            | wynik `dotnet publish` (katalog `artifacts/app`) |
| Czas przechowywania  | 7 dni                                            |
| Brak plików          | workflow kończy się błędem (`if-no-files-found: error`) |

Artefakt to wynik kompilacji przypisany do konkretnego uruchomienia workflow – nie jest to wdrożenie aplikacji.

### Gdzie szukać logów i artefaktów

1. Otwórz repozytorium na GitHubie i przejdź do zakładki **Actions** (albo do sekcji **Checks** w Pull Requeście).
2. Wybierz interesujące uruchomienie workflow.
3. **Logi:** otwórz job `build` i rozwiń wybrany step. Przy błędzie szukaj pierwszego czerwonego stepu i komunikatu zaczynającego się od `ERROR:`.
4. **Artefakt:** na stronie podsumowania uruchomienia przewiń do sekcji **Artifacts** i pobierz paczkę `app-<sha>`.

### Diagnostyka kontrolowanego błędu (Lab 08)

W ramach laboratorium na branchu `ci/lab08-workflow` celowo wywołano błąd pipeline'u:

- **Nieudany step:** Check repository structure
- **Komunikat:** `ERROR: Project file not found: ./src/ProjectThatDoesNotExist/Project.csproj`
- **Przyczyna:** `PROJECT_PATH` wskazywał nieistniejący plik
- **Skutek:** kolejne stepy (restore, build, publish, upload) zostały pominięte (`skipped`)
- **Naprawa:** przywrócenie poprawnej ścieżki do pliku `.csproj`

Historia commitów i uruchomień Actions zawiera zarówno commit wywołujący błąd, jak i commit z naprawą.

## Struktura repozytorium

```text
.
├── .github/
│   └── workflows/
│       └── build.yml
├── scripts/
│   └── check-repository.sh
├── src/
│   └── MyApp/
│       └── MyApp.csproj
└── README.md
```

## Autor

Hubert Michalski
