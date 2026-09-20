# Lab 9B: Interpretowanie modeli

## Po co jest to ćwiczenie?

W [Lab 9A](Lab09A.md) wyjaśnienia pojawiły się same - wystarczyło jedno ustawienie w zadaniu (job) automatycznego uczenia maszynowego. Modele, które piszesz samodzielnie, takiej wygody nie mają. Regresja logistyczna, drzewo decyzyjne, las losowy: każdy z nich zwraca przewidywanie i nic poza tym. Żeby dowiedzieć się, co wpłynęło na wynik, trzeba sięgnąć po osobne narzędzie.

Takim narzędziem jest **wyjaśniacz (ang. *explainer*)** - kod, który wielokrotnie podmienia wartości cech na wejściu modelu i obserwuje, jak zmienia się przewidywanie. Z tych obserwacji wylicza **ważność cech (ang. *feature importance*)**, czyli miarę wpływu każdej kolumny na wynik. Działa to niezależnie od tego, jakim algorytmem model został wytrenowany - dlatego takie wyjaśniacze nazywa się „czarnoskrzynkowymi".

To ćwiczenie ma dwie wyraźnie różne części:

- **Część lokalna** - biblioteka `interpret-community` uruchomiona wprost w notatniku, na modelu wytrenowanym w tym samym notatniku. Nie wymaga żadnego połączenia z Azure Machine Learning; ten sam kod zadziała na dowolnej maszynie.
- **Część w obszarze roboczym** - dołączenie wyjaśnień do modelu **zarejestrowanego** w Azure ML przez zbudowanie **Responsible AI dashboard**. To potok (pipeline) złożony z gotowych komponentów, który uruchamia się na klastrze i tworzy pulpit oglądany w Studio.

> **Po co dwie drogi**: pierwsza służy do własnej pracy - szybko, w pętli, przy analizie modelu, nad którym właśnie siedzisz. Druga służy do pokazania wyniku innym: wyjaśnienie zostaje przypięte do zarejestrowanego modelu, ma swój adres w Studio i przetrwa zamknięcie notatnika.

## Czego się nauczysz

1. Czym jest wyjaśniacz i dlaczego jeden rodzaj wyjaśniacza obsługuje wiele typów modeli.
2. Jak policzyć **globalną** ważność cech, czyli obraz zachowania modelu na całym zbiorze.
3. Jak policzyć **lokalną** ważność cech, czyli wyjaśnienie pojedynczego przewidywania.
4. Czym jest **Responsible AI dashboard** i z jakich komponentów powstaje.
5. Jak zbudować taki pulpit kreatorem w studio dla modelu zarejestrowanego w obszarze roboczym.
6. Jak odczytać w Studio wykresy **Aggregate feature importance** i **Individual feature importance**.

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz pozostałe zasoby używane w tym ćwiczeniu. Potrzebne jest też [Lab 3B](Lab03B.md), w którym rejestrowany jest model `diabetes_model` używany w drugiej części.

## Krok 1: Interpretowanie modelu lokalnie

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.

2. Gdy instancja obliczeniowa jest uruchomiona, kliknij przy niej link **JupyterLab**, aby otworzyć JupyterLab w nowej karcie przeglądarki.

3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md), a w nim notatnik **09B - Interpreting Models.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a następnie czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

   > **Dlaczego kernel jest ważny**: kernel decyduje, które środowisko Pythona wykonuje kod. Tylko kernel z SDK v2 ma zainstalowany pakiet `azure-ai-ml`, potrzebny w drugiej części ćwiczenia.

### Na co zwrócić uwagę w pierwszej części

**Model trenuje się zwykłym kodem scikit-learn.**

> **Dlaczego to ważne**: notatnik świadomie zaczyna od modelu, który nie ma nic wspólnego z Azure ML - nie powstał w zadaniu i nie jest zarejestrowany. Chodzi o pokazanie, że interpretowalność jest własnością modelu i danych, a nie usługą chmurową. Ten sam kod zadziała na laptopie.

**Wyjaśniacz `TabularExplainer`.**

> **Dlaczego akurat ten**: to wyjaśniacz czarnoskrzynkowy - nie zagląda do wnętrza modelu, tylko wielokrotnie go odpytuje i obserwuje reakcje na zmiany wejścia. Pod spodem dobiera odpowiednią odmianę metody SHAP. Konsekwencja jest praktyczna: podmiana drzewa decyzyjnego na dowolny inny klasyfikator nie wymaga zmiany kodu wyjaśniania.

**Wyjaśnienie globalne (`explain_global`).**

> **Co z niego wynika**: dostajesz uszeregowaną listę cech - od tej, która najmocniej przesuwa przewidywania w całym zbiorze, do najsłabszej. To odpowiedź na pytanie „na co ten model w ogóle patrzy". Przydaje się przy sprawdzaniu, czy model nie oparł się na kolumnie, która nie powinna go obchodzić.

**Wyjaśnienie lokalne (`explain_local`).**

> **Czym się różni od globalnego**: wyjaśnienie lokalne dotyczy konkretnego pacjenta, nie całego zbioru, i podaje wkład każdej cechy osobno dla każdej możliwej etykiety. Cecha nieistotna globalnie może być rozstrzygająca dla pojedynczego przypadku. To właśnie tego rodzaju wyjaśnienia oczekuje osoba, której decyzja dotyczy: nie „co model uważa za ważne ogólnie", tylko „dlaczego akurat mnie zakwalifikował w ten sposób".

## Krok 2: Zbudowanie pulpitu Responsible AI dla zarejestrowanego modelu

Pulpit powstaje dla modelu `diabetes_model` zarejestrowanego w [Lab 3B](Lab03B.md). Zbudujesz go **kreatorem w Azure Machine Learning studio**, bez pisania kodu.

1. Uruchom komórkę notatnika, która sprawdza, czy model i zasób danych są na miejscu - wypisze ich nazwy i wersje.

2. W studio przejdź do **Models**, wybierz **diabetes_model**, a na karcie **Details** kliknij **Create Responsible AI dashboard (preview)**.

3. Przejdź przez kreator:

   | Sekcja | Wybór |
   |---|---|
   | Training dataset | `diabetes_mltable` |
   | Test dataset | `diabetes_mltable` |
   | Modeling task | Classification |
   | Dashboard components | Model debugging |
   | Component parameters | Target feature: `Diabetic`, **Generate explanations** włączone |
   | Experiment configuration | nazwa pulpitu, eksperyment, klaster `aml-cluster` |

4. Kliknij **Create**. Zadanie potrwa kilkanaście minut.

### Co kreator robi pod spodem

Uruchamia potok złożony z gotowych komponentów opublikowanych przez Microsoft w rejestrze `azureml`:

| Komponent | Rola |
|---|---|
| `microsoft_azureml_rai_tabular_insight_constructor` | tworzy pusty pulpit dla wskazanego modelu i danych |
| `microsoft_azureml_rai_tabular_explanation` | dokłada do niego ważność cech liczoną metodą SHAP |
| `microsoft_azureml_rai_tabular_insight_gather` | składa gotowy pulpit z zebranych wyników |

> **Dlaczego przez kreator, a nie z SDK**: te same komponenty da się pobrać kodem i połączyć we własny potok - tak opisuje to [dokumentacja](https://learn.microsoft.com/azure/machine-learning/how-to-responsible-ai-insights-sdk-cli). Nie w każdym obszarze roboczym są jednak dostępne: `registry_client.components.get()` kończy się wtedy błędem `Could not find component with name`. Kreator działa niezależnie od tego, więc ćwiczenie korzysta z niego.

> **Dlaczego to potok, a nie jedna funkcja**: liczenie wyjaśnień dla całego zbioru jest kosztowne obliczeniowo, a poszczególne etapy dają się wymieniać - obok wyjaśnień do pulpitu można dołożyć analizę błędów czy analizę przyczynową. Rozbicie na komponenty pozwala uruchomić to na klastrze i dobrać zestaw narzędzi do potrzeb.

### Wymagania formatów

> **Dlaczego to jest częsta pułapka**: komponenty Responsible AI przyjmują dane wyłącznie w formacie `mltable`, a model wyłącznie w formacie MLflow. Dlatego w kreatorze wybierasz zasób `diabetes_mltable` (typu `mltable`) oraz `diabetes_model` zarejestrowany w formacie MLflow w [Lab 3B](Lab03B.md). Zwykły plik CSV albo model zapisany przez `joblib` nie pojawią się nawet na liście wyboru.

## Krok 3: Obejrzenie wyjaśnień w Studio

1. Gdy zadanie się zakończy, wróć do modelu **diabetes_model** i otwórz jego kartę **Responsible AI**. Wybierz utworzony pulpit.

2. Obejrzyj wykres **Aggregate feature importance**.

   > **Co tu widzisz**: to jest wyjaśnienie globalne - odpowiednik tego, co w pierwszej części ćwiczenia policzył `TabularExplainer`, tylko wyliczony jako zarządzane zadanie i przypięty na stałe do zarejestrowanego modelu. Warto porównać kolejność cech z listą wypisaną wcześniej w notatniku: obie powinny wskazywać podobnych zwycięzców, choć liczby nie muszą być identyczne, bo modele i zbiory są inne.

3. Przełącz się na widok **Individual feature importance** i wskaż pojedynczy punkt danych, aby zobaczyć wyjaśnienie tego konkretnego przewidywania.

   > **Dlaczego to robimy**: to najbardziej praktyczna część pulpitu. Pozwala wziąć jeden przypadek - najlepiej taki, w którym model się pomylił - i zobaczyć, która cecha przeważyła szalę. Systematyczne przeglądanie takich przypadków szybciej podpowiada, co poprawić w danych, niż patrzenie na uśrednione metryki.

4. Zajrzyj do sekcji **Error analysis** - pokazuje, w których podgrupach danych model myli się najczęściej.

> **Uwaga**: Jeśli zamierzasz od razu przejść do [kolejnego ćwiczenia](Lab10A.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.

## Co dalej

Model jest już wytrenowany, wdrożony i zrozumiany. Zostaje ostatni etap cyklu życia: obserwowanie go po uruchomieniu w produkcji. W [Lab 10A](Lab10A.md) włączysz zbieranie telemetrii i danych produkcyjnych dla wdrożonego punktu końcowego (endpoint) - czyli zbudujesz podstawę, bez której nie da się stwierdzić, że model przestał działać poprawnie.
