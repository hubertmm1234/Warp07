Continuous Integration
(Optional) Status build: [![CI](https://github.com/<WŁAŚCICIEL>/<NAZWA_REPO>/actions/workflows/<NAZWA_WORKFLOW>/badge.svg)](https://github.com/<WŁAŚCICIEL>/<NAZWA_REPO>/actions/workflows/<NAZWA_WORKFLOW>)

Wyzwalacze (workflow triggers)
Workflow uruchamia się:

przy push do gałęzi (np. main)
przy pull request (dla PR-ów kierowanych do main)
Sprawdzany projekt i wersja .NET
Nazwa projektu: <NAZWA_PROJEKTU>
Używana wersja .NET: <DOTNET_VERSION> (np. 8.0.x)
Kontrole wykonywane w pipeline
W ramach workflow wykonywane są kolejno kontrole:

Struktura repozytorium
sprawdzany jest obecny i niepusty plik README.md
walidowana jest ścieżka przekazana jako argument (np. PROJECT_PATH) oraz to, czy wskazuje na istniejący plik projektu
Restore (przywrócenie zależności)
przywracane są paczki NuGet dla projektu
Build (kompilacja)
projekt jest budowany w trybie Release
Run lub Publish
pipeline uruchamia aplikację testowo (run) lub generuje artefakty przez publish (w zależności od konfiguracji w repo)
Artefakty
Nazwa artefaktu: <ARTEFAKT_NAME> (np. build-artifacts)
Czas przechowywania: 7 dni
Artefakty są zapisywane po etapie publish/build (zgodnie z tym, co jest skonfigurowane w workflow).
Logi i artefakty na GitHubie
Logi jobów: przejdź do zakładki Actions w repozytorium i otwórz uruchomienie workflow — tam zobaczysz logi dla każdego kroku.
Artefakty: w tym samym uruchomieniu workflow wybierz sekcję Artifacts (lub “Workflow run artifacts”), gdzie pobierzesz zbudowane pliki.
