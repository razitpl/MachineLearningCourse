# Lab 8A: Strojenie hiperparametrów

## Po co jest to ćwiczenie?

**Hiperparametry** to wartości, które sterują przebiegiem trenowania, ale nie wynikają z danych - trzeba je ustawić z góry. Siła regularyzacji w regresji logistycznej, maksymalna głębokość drzewa, tempo uczenia i rozmiar paczki w sieci neuronowej: wszystko to są hiperparametry. Model nie potrafi ich sam wyznaczyć, bo są ustalane, zanim zacznie się uczyć.

Kłopot w tym, że ich dobór potrafi rozstrzygnąć o jakości modelu. Ta sama regresja logistyczna z różną siłą regularyzacji daje inne AUC, a różnica bywa większa niż zysk z podmiany algorytmu. Typowa reakcja to metoda prób i błędów: zmienić wartość, uruchomić trenowanie, zapisać wynik w zeszycie, powtórzyć. Przy sześciu wartościach to sześć uruchomień pod rząd i sześć okazji, żeby pomylić się przy notowaniu.

Azure Machine Learning ma na to **zadanie przeglądu (sweep)**: opisujesz raz, jakie wartości mają zostać sprawdzone i którą metrykę maksymalizujesz, a Azure ML uruchamia trenowania równolegle na klastrze, zbiera metryki i wskazuje najlepszą próbę. Z ręcznej dłubaniny robi się jedno zadanie (job).

## Czego się nauczysz

1. Czym są hiperparametry i dlaczego nie da się ich wyznaczyć z danych treningowych.
2. Jak przygotować skrypt treningowy przyjmujący hiperparametr jako argument wiersza poleceń.
3. Czym jest **przestrzeń hiperparametrów** i jak opisać ją w kodzie.
4. Jak skonfigurować zadanie przeglądu (sweep): metoda próbkowania, metryka docelowa, limity liczby prób.
5. Jak odnaleźć najlepszą próbę przy pomocy MLflow i zarejestrować model, który z niej powstał.

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz pozostałe zasoby używane w tym ćwiczeniu. Przydatne będzie też [Lab 3A](Lab03A.md), w którym omówione są zadania i logowanie metryk przez MLflow.

## Krok 1: Strojenie hiperparametrów

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.

2. Gdy instancja obliczeniowa jest uruchomiona, kliknij przy niej link **JupyterLab**, aby otworzyć JupyterLab w nowej karcie przeglądarki.

3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md), a w nim notatnik **08A - Tuning Hyperparameters.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a następnie czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

   > **Dlaczego kernel jest ważny**: kernel decyduje, które środowisko Pythona wykonuje kod. Tylko kernel z SDK v2 ma zainstalowany pakiet `azure-ai-ml`.

## Krok 2: Na co zwrócić uwagę w notatniku

### Przygotowanie zasobu danych

Notatnik łączy `data/diabetes.csv` i `data/diabetes2.csv` w jedną tabelę MLTable i rejestruje ją jako zasób danych `diabetes_mltable`.

> **Dlaczego to robimy**: MLTable opisuje nie tylko lokalizację danych, ale też sposób ich odczytu, więc skrypt treningowy dostaje gotową ramkę danych bez zgadywania separatora czy nagłówków. Ten sam zasób wykorzysta [Lab 8B](Lab08B.md) - AutoML przyjmuje na wejściu wyłącznie `mltable`.

### Skrypt treningowy

`diabetes_training.py` różni się od zwykłego skryptu dwiema rzeczami:

1. przyjmuje `--regularization` jako argument wiersza poleceń,
2. loguje przez MLflow metryki `AUC` i `Accuracy`.

> **Dlaczego hiperparametr musi być argumentem**: zadanie przeglądu uruchamia ten sam skrypt wielokrotnie, za każdym razem podstawiając inną wartość. Gdyby siła regularyzacji była wpisana w kodzie na stałe, nie byłoby czego zmieniać.

> **Dlaczego logowanie metryki jest obowiązkowe**: to na podstawie zalogowanej metryki Azure ML porównuje próby i wybiera najlepszą. Skrypt, który nic nie loguje, uniemożliwia rozstrzygnięcie, które ustawienie wygrało. Notatnik loguje dwie metryki - dzięki temu można wybrać, którą optymalizować, a drugą obejrzeć dla porównania.

### Klaster obliczeniowy

> **Dlaczego to musi być klaster**: na tym polega przewaga chmury w tym zadaniu. Sześć trenowań uruchomionych po kolei trwa sześć razy dłużej niż jedno; sześć trenowań rozdzielonych na cztery węzły kończy się prawie tak szybko jak dwa. Skoro próby są od siebie niezależne, nie ma powodu czekać na jedną, żeby zacząć następną.

### Definicja przeglądu

Kod najpierw definiuje zwykłe zadanie `command` (skrypt, dane, środowisko, klaster), a dopiero potem nakłada na nie przestrzeń hiperparametrów i ustawienia przeglądu.

> **Dlaczego taka kolejność**: zadanie przeglądu nie jest osobnym bytem - to zwykłe zadanie treningowe uruchamiane wielokrotnie z podmienionym parametrem. Rozumiejąc tę konstrukcję, potrafisz zamienić w przegląd dowolne własne trenowanie: wystarczy wystawić parametr na zewnątrz i opisać, jakie wartości ma przyjmować.

Najważniejsze ustawienia:

| Ustawienie | Co znaczy |
|---|---|
| `Choice(values=[0.001, ... , 1.0])` | **przestrzeń hiperparametrów**, czyli zbiór wartości do sprawdzenia |
| `sampling_algorithm="grid"` | sposób wybierania kombinacji - siatka sprawdza wszystkie po kolei |
| `primary_metric="AUC"` | nazwa metryki decydującej o wyniku; musi dokładnie odpowiadać nazwie logowanej w skrypcie |
| `goal="Maximize"` | kierunek optymalizacji |
| `max_total_trials=6` | ile prób może powstać łącznie |
| `max_concurrent_trials=4` | ile z nich działa równolegle |

> **Dlaczego siatka akurat tutaj**: hiperparametr jest jeden i ma sześć wartości, więc sprawdzenie wszystkich jest tanie i daje pełny obraz. Gdyby parametrów były trzy, siatka oznaczałaby iloczyn wszystkich kombinacji i liczba prób wybuchłaby - wtedy sięga się po próbkowanie losowe albo bayesowskie, które szuka dobrego rozwiązania bez przeglądania całości.

> **Dlaczego limity są potrzebne**: każda próba zużywa czas maszyn, za które płacisz. `max_total_trials` chroni przed rachunkiem za eksperyment, który wymknął się spod kontroli, a `max_concurrent_trials` dopasowuje tempo do wielkości klastra - ustawienie większej liczby równoległych prób niż węzłów nic nie przyspieszy.

### Wybór najlepszej próby

Po zakończeniu przeglądu notatnik pobiera przez MLflow wszystkie próby potomne, sortuje je malejąco po AUC i bierze pierwszą.

> **Dlaczego warto obejrzeć całą tabelę, nie tylko zwycięzcę**: układ wyników mówi więcej niż jedna liczba. Jeśli AUC rośnie aż do skraju przeszukiwanego zakresu, prawdziwe optimum leży najpewniej poza nim i warto rozszerzyć przestrzeń. Jeśli wszystkie próby dają niemal identyczny wynik, ten hiperparametr po prostu nie ma tu znaczenia i strojenie go jest stratą czasu.

Te same dane zobaczysz w interfejsie: otwórz zadanie w Azure Machine Learning studio i przejdź na kartę **Child jobs**.

### Rejestracja zwycięskiego modelu

Ostatnia komórka rejestruje model wytrenowany w najlepszej próbie jako `diabetes_model`.

> **Dlaczego to domyka całość**: przegląd bez rejestracji zostawia tylko notatkę w rodzaju „ta wartość wyszła najlepiej". Rejestracja zamienia najlepszą próbę w nazwany, wersjonowany zasób z zapisanymi metrykami - gotowy do wdrożenia dokładnie tak, jak w [Lab 7A](Lab07A.md).

> **Uwaga o formacie**: rejestrowany tu model jest w formacie MLflow (`MLFLOW_MODEL`). Ćwiczenia 7A i 7B rejestrują własną wersję `diabetes_model` zapisaną przez `joblib`, bo tego wymagają ich skrypty scoringowe. Rejestr modeli przechowuje kolejne wersje, więc nic się nie nadpisuje - warto tylko wiedzieć, która wersja ma jaki format.

> **Uwaga**: Jeśli zamierzasz od razu przejść do [kolejnego ćwiczenia](Lab08B.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.

## Co dalej

Przegląd z tego ćwiczenia automatyzuje dobór wartości parametru, ale algorytm i sposób przygotowania danych nadal wybierasz samodzielnie. W [Lab 8B](Lab08B.md) pójdziesz o poziom wyżej: automatyczne uczenie maszynowe przetestuje różne algorytmy wraz z ich przekształceniami danych i samo wskaże najlepsze połączenie.
