# Słowniczek i konwencje polskiej wersji kursu

Ten plik opisuje zasady tłumaczenia stosowane w polskiej wersji materiałów. Trzymanie się ich sprawia, że kurs czyta się spójnie i że student nie musi zgadywać, czy „zadanie" oznacza krok ćwiczenia, czy obiekt w Azure ML.

## Terminologia

| Angielski | Polski | Uwagi |
|---|---|---|
| lab | ćwiczenie | W nagłówkach zostaje „Lab 3A" (spójność z nazwami plików i linkami). |
| Task (sekcja ćwiczenia) | **Krok** | Nigdy „Zadanie" - to słowo jest zarezerwowane dla *job*. |
| job | **zadanie** | Przy pierwszym użyciu w dokumencie: „zadanie (job)". |
| run (SDK v1) | przebieg | Pojawia się tylko w kontekście porównań z v1. |
| experiment | eksperyment | W v2 to już tylko etykieta grupująca zadania (`experiment_name`). |
| workspace | obszar roboczy | |
| compute instance | instancja obliczeniowa | Twoja maszyna deweloperska w chmurze. |
| compute cluster | klaster obliczeniowy | Maszyny uruchamiane na czas zadania i gaszone po nim. |
| compute target | środowisko obliczeniowe | Ogólne określenie „gdzie ma się wykonać kod". |
| datastore | magazyn danych | Połączenie do konta storage. |
| data asset | zasób danych | Nazwana, wersjonowana referencja do danych. |
| environment | środowisko | W sensie: obraz kontenera + pakiety. |
| pipeline | potok | Przy pierwszym użyciu: „potok (pipeline)". |
| component | komponent | Krok potoku. |
| endpoint | punkt końcowy | Przy pierwszym użyciu: „punkt końcowy (endpoint)", dalej krótko „endpoint". |
| deployment | wdrożenie | |
| to register (model/data) | zarejestrować | |
| to deploy | wdrożyć | |
| sweep job | zadanie przeglądu (sweep) | Strojenie hiperparametrów. |
| data drift | dryf danych | |
| model monitoring | monitorowanie modelu | |

## Zasady stylu

1. **Elementy interfejsu zostają po angielsku**, pogrubione: `**Compute**`, `**Compute instances**`, `**Running**`, `**Stop**`, `**Endpoints**`. Studio jest po angielsku - tłumaczenie etykiet utrudniłoby ich znalezienie.
2. **Nazwy zasobów kursu nigdy się nie tłumaczą**: `aml-cluster`, `mymachine`, `diabetes_dataset`, `diabetes_model`, `diabetes-endpoint`, `blue`.
3. **Bez form typu „ukończyłeś/aś"**. Zamiast tego forma bezosobowa lub 2. os. neutralna: „upewnij się, że masz ukończone", „po sklonowaniu repozytorium".
4. **Bez kalek z angielskiego**: nie „wybierz odnośnik", tylko „kliknij link"; nie „data scientist'a", tylko „osoby pracującej z danymi" albo „analityka danych".
5. **Nazwy techniczne w kodzie zostają**: klasy, metody, parametry, wartości enumów (`type="amlcompute"`, `auth_mode="key"`), nazwy kolumn (`Diabetic`, `BMI`, ...), ścieżki, URL-e, nazwa kernela `Python 3.10 - SDK v2`.
6. **Każdy krok wyjaśnia „po co"**. Sama instrukcja „kliknij X" niczego nie uczy - obok powinna być ramka `> **Dlaczego to robimy**: ...`.

## Struktura dokumentu ćwiczenia

Każdy dokument w tym katalogu ma układ:

1. `# Lab XY: Tytuł`
2. `## Po co jest to ćwiczenie?` - jaki problem rozwiązujemy i gdzie to leży w cyklu życia ML.
3. `## Czego się nauczysz` - konkretne efekty uczenia się.
4. `## Zanim zaczniesz` - wymagania wstępne.
5. `## Krok 1`, `## Krok 2`, ... - instrukcje, każda z ramką „Dlaczego to robimy".
6. `## Co dalej` - powiązanie z kolejnym ćwiczeniem.
