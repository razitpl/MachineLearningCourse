# Lab 10A: Monitorowanie modelu

## Po co jest to ćwiczenie?

Wdrożenie modelu jako usługi nie kończy pracy - zaczyna nowy jej etap. Od tej chwili model przetwarza prawdziwe zapytania, a Ty nie widzisz ich z notatnika. Pojawiają się pytania, na które kod źródłowy nie odpowie: ile zapytań przychodzi, jak długo trwa odpowiedź, czy któreś kończą się błędem, jakie dane właściwie trafiają na wejście.

Odpowiedzi daje **telemetria**, czyli dane o działaniu usługi zbierane automatycznie w trakcie jej pracy: czasy odpowiedzi, liczniki zapytań, komunikaty z logów. Azure Machine Learning udostępnia dla zarządzanego punktu końcowego (endpoint) dwa uzupełniające się mechanizmy:

- **Integracja z Application Insights** - ustawienie na poziomie wdrożenia (`app_insights_enabled=True`), które wysyła wbudowane dane o zapytaniach i czasach odpowiedzi, a także wszystko, co skrypt scoringowy wypisze na standardowe wyjście, do zasobu Application Insights powiązanego z obszarem roboczym.
- **Monitorowanie modelu (ang. *model monitoring*)** - szersza możliwość zbudowana na funkcji **zbierania danych produkcyjnych**, śledząca dryf danych, dryf przewidywań i jakość danych w czasie. Samo monitorowanie modelu jest tematem [Lab 10B](Lab10B.md); **to ćwiczenie włącza zbieranie danych, bez którego tamto nie ma czego analizować**.

> **Jak te dwie rzeczy się mają do siebie**: Application Insights odpowiada na pytanie „czy usługa działa" - to diagnostyka techniczna. Monitorowanie modelu odpowiada na pytanie „czy usługa nadal ma sens" - czy dane, które przychodzą dzisiaj, przypominają jeszcze te, na których model się uczył. Pierwsze wykryje awarię w kilka minut, drugie wykryje powolne psucie się przewidywań w ciągu tygodni.

## Czego się nauczysz

1. Czym jest telemetria wdrożenia i czym różni się od metryk trenowania.
2. Jak włączyć diagnostykę Application Insights przy tworzeniu wdrożenia.
3. Czym są **dane produkcyjne** i jak je zbierać klasą `Collector` z pakietu `azureml-ai-monitoring`.
4. Jak napisać skrypt scoringowy, który poza zwracaniem przewidywań rejestruje swoje wejścia i wyjścia.
5. Jak obejrzeć zebraną telemetrię - na karcie **Monitoring** punktu końcowego oraz w zasobie Application Insights w portalu Azure.

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz pozostałe zasoby używane w tym ćwiczeniu. Warto też mieć za sobą [Lab 7A](Lab07A.md), w którym powstaje punkt końcowy `diabetes-endpoint` używany tutaj ponownie - notatnik utworzy go samodzielnie, jeśli jeszcze nie istnieje.

## Krok 1: Wdrożenie modelu z włączoną telemetrią

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.

2. Gdy instancja obliczeniowa jest uruchomiona, kliknij przy niej link **JupyterLab**, aby otworzyć JupyterLab w nowej karcie przeglądarki.

3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md), a w nim notatnik **10A - Monitoring a Model.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a następnie czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

   > **Dlaczego kernel jest ważny**: kernel decyduje, które środowisko Pythona wykonuje kod. Tylko kernel z SDK v2 ma zainstalowany pakiet `azure-ai-ml`.

## Krok 2: Na co zwrócić uwagę w notatniku

### Model i jego rejestracja

Notatnik trenuje prosty klasyfikator drzewa decyzyjnego i rejestruje go jako `diabetes_model`.

> **Dlaczego to robimy tutaj jeszcze raz**: ćwiczenie ma być samowystarczalne - jeśli model był już rejestrowany wcześniej, powstaje po prostu kolejna jego wersja, a nic się nie nadpisuje. Uwaga ćwiczenia leży nie na jakości modelu, tylko na tym, co dzieje się z nim po wdrożeniu.

### Skrypt scoringowy

Skrypt `score_diabetes.py` robi trzy rzeczy naraz: zwraca przewidywania, wypisuje dane każdego zapytania na standardowe wyjście i rejestruje wejścia oraz wyjścia klasą `Collector` z pakietu `azureml-ai-monitoring`.

> **Dlaczego wypisywanie na wyjście ma sens**: wszystko, co skrypt wypisze, trafia do Application Insights i daje się później przeszukać zapytaniem. To najprostsza droga, żeby zobaczyć, jakie dane realnie przychodzą do usługi - bez modyfikowania aplikacji klienckiej i bez zgadywania.

> **Dlaczego `Collector` to co innego niż wypisywanie**: tekst w logach nadaje się do czytania przez człowieka, ale nie do analizy statystycznej. `Collector` zapisuje wejścia i wyjścia w uporządkowanej, tabelarycznej postaci do magazynu danych obszaru roboczego. Dopiero takie dane da się porównać ze zbiorem treningowym - i dokładnie z nich skorzysta monitor dryfu w [Lab 10B](Lab10B.md).

> **Dlaczego pakiet musi być w środowisku**: `azureml-ai-monitoring` znajduje się na liście zależności w pliku środowiska. Bez niego kontener wdrożenia nie wystartuje, bo skrypt nie zaimportuje klasy `Collector`.

### Konfiguracja wdrożenia

Wdrożenie `blue` punktu końcowego `diabetes-endpoint` powstaje z dwoma ustawieniami: `app_insights_enabled=True` oraz `data_collector`.

> **Dlaczego oba naraz**: te ustawienia można włączyć tylko przy tworzeniu albo aktualizacji wdrożenia - nie da się ich dołożyć do działającej usługi bez ponownego wdrożenia. Dlatego warto zdecydować się na nie od razu. Konsekwencja dla dalszej części kursu jest wprost praktyczna: gdyby pominąć `data_collector`, [Lab 10B](Lab10B.md) nie miałby żadnych danych produkcyjnych do porównania i monitor dryfu nie pokazałby nic.

> **Uwaga o czasie**: tworzenie wdrożenia trwa kilka minut - kod czeka, aż wdrożenie zgłosi się jako sprawne.

### Wywołanie usługi

Notatnik wysyła do punktu końcowego przykładowe dane pacjentów i wypisuje przewidywania.

> **Dlaczego to jest potrzebne do monitorowania**: świeżo wdrożona usługa nie ma żadnego ruchu, więc nie ma też żadnej telemetrii. Każde wywołanie wytwarza wpis w Application Insights i wiersz w zbieranych danych produkcyjnych. Bez wywołań wszystkie wykresy w kolejnym kroku byłyby puste.

> **Wskazówka**: jeśli pierwsze wywołanie zwróci błąd, wdrożenie może jeszcze nie być gotowe - odczekaj kilkanaście sekund i spróbuj ponownie.

## Krok 3: Obejrzenie telemetrii

1. W [Azure Machine Learning studio](https://ml.azure.com) otwórz stronę punktu końcowego `diabetes-endpoint` i przejdź na kartę **Monitoring**, aby zobaczyć wbudowane wykresy liczby zapytań i czasów odpowiedzi.

   > **Dlaczego zaczynamy stąd**: to najszybszy sposób, żeby potwierdzić, że Application Insights w ogóle odbiera dane. Jeśli wykresy są puste, nie ma sensu układać zapytań w logach - problem leży wcześniej, po stronie konfiguracji wdrożenia albo braku ruchu.

2. W [portalu Azure](https://portal.azure.com) otwórz swój obszar roboczy Machine Learning i na stronie **Overview** kliknij powiązany zasób **Application Insights**.

   > **Dlaczego to jest osobny zasób**: Application Insights powstaje razem z obszarem roboczym, ale jest samodzielną usługą Azure Monitor. Tam trafia telemetria i tam mieszka pełny język zapytań - Studio pokazuje tylko wybrane, gotowe wykresy.

3. W zasobie Application Insights wybierz **Logs** i przejrzyj zapisane ślady zapytań. Przy pierwszym otwarciu Log Analytics może być konieczne kliknięcie **Get Started**.

   > **Dlaczego warto tam zajrzeć**: w logach znajdziesz to, co skrypt scoringowy wypisał dla każdego zapytania, łącznie z danymi wejściowymi i zwróconym przewidywaniem. To jedyne miejsce, w którym da się prześledzić pojedyncze wywołanie od danych po odpowiedź - nieocenione przy zgłoszeniu w rodzaju „usługa zwróciła dziwny wynik dla tego pacjenta".

   > **Uwaga o opóźnieniu**: telemetria pojawia się z opóźnieniem rzędu kilku minut. Pusta lista tuż po wysłaniu zapytań nie oznacza błędu konfiguracji - warto odczekać i odświeżyć.

> **Uwaga**: Jeśli zamierzasz od razu przejść do [kolejnego ćwiczenia](Lab10B.md), zostaw instancję obliczeniową uruchomioną i **nie usuwaj punktu końcowego** - [Lab 10B](Lab10B.md) korzysta z tego samego wdrożenia i z danych, które właśnie zaczęło ono zbierać. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.

## Co dalej

Telemetria z tego ćwiczenia mówi, czy usługa żyje. Nie powie jednak, czy model nadal przewiduje sensownie - usługa może odpowiadać bez zarzutu i w milisekundach, podając przy tym coraz gorsze wyniki, bo dane pacjentów przestały przypominać te sprzed roku. W [Lab 10B](Lab10B.md) użyjesz danych produkcyjnych, które zaczęło właśnie zbierać to wdrożenie, do wykrywania **dryfu danych** i ustawisz harmonogram, który będzie sprawdzał to regularnie za Ciebie.
