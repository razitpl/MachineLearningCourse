# Lab 9A: Przeglądanie wyjaśnień modeli z automatycznego uczenia maszynowego

## Po co jest to ćwiczenie?

Model, który dobrze przewiduje, to jeszcze nie model, któremu można zaufać. Przy przewidywaniu cukrzycy pytanie „dlaczego akurat ten pacjent?" pada natychmiast - i nie jest to ciekawość, tylko warunek dopuszczenia modelu do użycia. Podobnie dzieje się wszędzie tam, gdzie decyzja dotyczy człowieka: przy ocenie wniosku kredytowego, przy kolejności przyjęć do lekarza, przy rekrutacji.

Stąd pojęcie **wyjaśnialności modelu (ang. *model explainability*)**: zestaw metod, które pokazują, na czym model opiera swoje decyzje, zamiast traktować go jak czarną skrzynkę. Najczęściej używanym narzędziem jest **ważność cech (ang. *feature importance*)** - liczba mówiąca, jak mocno każda kolumna danych wpływa na przewidywanie.

W [Lab 8B](Lab08B.md) automatyczne uczenie maszynowe wybrało najlepszy model za Ciebie. Tutaj zobaczysz, że przy okazji potrafi też wyjaśnić, co ten model uznał za istotne - wystarczy jedno ustawienie w konfiguracji zadania (job).

> **Gdzie ogląda się wyjaśnienia**: wyniki trafiają na kartę **Explanations (preview)** w Azure Machine Learning studio. SDK nie daje sposobu, żeby wciągnąć te dane z powrotem do notatnika - dlatego w tym ćwiczeniu wyjaśnienia przegląda się w interfejsie, a nie wypisuje w Pythonie. Notatnik służy do uruchomienia zadania i wypisania linku.
>
> Pełny **Responsible AI dashboard** - z analizą błędów, sprawiedliwością i analizą przyczynową - **nie jest dostępny dla modeli AutoML**; studio pokazuje tam komunikat „Responsible AI dashboard is currently not supported for AutoML models". Taki pulpit zbudujesz w [Lab 9B](Lab09B.md), dla modelu wytrenowanego zwykłym skryptem.

## Czego się nauczysz

1. Co znaczy ważność cech i czym różni się od zwykłej korelacji.
2. Jak włączyć generowanie wyjaśnień w zadaniu automatycznego uczenia maszynowego (`enable_model_explainability=True`).
3. Jak otworzyć kartę **Explanations (preview)** najlepszego modelu i odczytać wykres ważności cech.
4. Czym jest **inżynieria cech (ang. *feature engineering*)** wykonywana automatycznie przez AutoML i jak wpływa na to, co widać w wyjaśnieniach.
5. Jak porównać ważność cech surowych (**Raw features**) z cechami wytworzonymi automatycznie (**Engineered features**).

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz pozostałe zasoby używane w tym ćwiczeniu. Przydatne będzie też [Lab 8B](Lab08B.md), w którym omówione jest samo automatyczne uczenie maszynowe.

Zadanie korzysta z zasobu danych `diabetes_mltable` (typ `mltable`) oraz z klastra obliczeniowego `aml-cluster`.

## Krok 1: Uruchomienie zadania z włączonymi wyjaśnieniami

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.

   > **Dlaczego to robimy**: instancja obliczeniowa jest maszyną, na której działa notatnik i z której zlecasz zadanie. Samo trenowanie wykona się na klastrze `aml-cluster`, więc instancja tylko wydaje polecenia.

2. Gdy instancja obliczeniowa jest uruchomiona, kliknij przy niej link **JupyterLab**, aby otworzyć JupyterLab w nowej karcie przeglądarki.

3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md), a w nim notatnik **09A - Reviewing Automated Machine Learning Explanations.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a następnie czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

   > **Dlaczego kernel jest ważny**: kernel decyduje, które środowisko Pythona wykonuje kod. Tylko kernel z SDK v2 ma zainstalowany pakiet `azure-ai-ml`.

## Krok 2: Na co zwrócić uwagę w notatniku

### Jedno ustawienie, które robi całą robotę

W ustawieniach trenowania zadania AutoML pojawia się `enable_model_explainability=True`.

> **Dlaczego to robimy**: bez tego ustawienia dostajesz sam model i jego metryki. Z nim Azure Machine Learning po wyłonieniu zwycięzcy uruchamia dodatkowe obliczenia, które mierzą wkład każdej cechy w przewidywanie, i zapisuje wynik na karcie **Explanations (preview)** tego modelu. Liczenie wyjaśnień kosztuje czas maszyn, dlatego jest opcjonalne - ale przy modelu, który ma trafić do użycia, warto je włączyć od razu, żeby nie wracać do tego później.

### Mały budżet prób

Zadanie ma ograniczoną liczbę prób i limit czasu.

> **Dlaczego to robimy**: celem tego ćwiczenia nie jest wygranie rankingu jakości, tylko obejrzenie wyjaśnień. Mały budżet skraca czekanie i rachunek, a wyjaśnienia wyglądają tak samo niezależnie od tego, czy model wybierano spośród pięciu, czy spośród pięćdziesięciu kandydatów.

### Link do Studio

Ostatnia komórka każdego etapu pobiera ukończone zadanie i wypisuje adres `Studio URL`.

> **Dlaczego akurat link**: to jedyna droga do wyjaśnień. Nie istnieje metoda SDK, która zwróciłaby ważność cech do zmiennej w Pythonie - dane wyjaśnień żyją w pulpicie w Studio. Jeśli spodziewasz się w notatniku tabelki z liczbami, to jej tam nie będzie i nie jest to usterka.

## Krok 3: Odczytanie ważności cech w Studio

1. Otwórz link wypisany przez notatnik, aby przejść do zadania w Azure Machine Learning studio.

2. Na karcie **Models + child jobs** wybierz najlepszy model, a następnie otwórz jego kartę **Explanations (preview)**. Jeśli karta jest pusta, zaznacz model i kliknij **Explain model**, wskazując klaster obliczeniowy - wyliczenie wyjaśnień potrwa kilka minut.

3. Obejrzyj wykres ważności cech: cechy są uszeregowane od najmocniej do najsłabiej wpływającej na przewidywanie.

   > **Dlaczego to robimy**: ten wykres odpowiada na pytanie, którego nie odpowie żadna metryka jakości - na czym model faktycznie opiera decyzje. Dwa modele z takim samym AUC mogą patrzeć na zupełnie różne kolumny, a to, na którą patrzą, przesądza o tym, czy będą działać poprawnie na nowych pacjentach.

   > **Jak to czytać ostrożnie**: ważność cech mówi, z czego model korzysta, a nie co jest przyczyną choroby. Cecha może być ważna, bo faktycznie niesie informację, ale też dlatego, że przypadkiem towarzyszy wynikowi w danych treningowych. Kolumna `PatientID` jest tu wzorcowym przykładem: jest identyfikatorem, nigdy nie powinna trafić do modelu jako cecha, a gdyby trafiła i wyszła na wysokiej pozycji, byłby to sygnał przecieku danych, a nie odkrycie medyczne.

## Krok 4: Cechy surowe a cechy wytworzone automatycznie

Notatnik uruchamia zadanie drugi raz, tym razem z włączonym przetwarzaniem wstępnym danych (`set_featurization`).

> **Dlaczego to robimy**: **inżynieria cech** to tworzenie nowych kolumn z już istniejących - skalowanie wartości, kodowanie kategorii, łączenie kolumn. AutoML potrafi zrobić to za Ciebie, co zwykle poprawia wynik, ale zmienia obraz w wyjaśnieniach: model uczy się już nie na oryginalnych kolumnach, tylko na tym, co z nich powstało.

> **Uwaga o nazewnictwie**: przekształcenia wykonują potoki transformacji scikit-learn. To zupełnie co innego niż potoki (pipelines) Azure Machine Learning z ćwiczeń [Lab 6A](Lab06A.md) i [Lab 6B](Lab06B.md) - zbieżność nazw jest przypadkowa.

1. Otwórz drugie zadanie przez link wypisany przez notatnik i przejdź do karty **Explanations**.

2. Odszukaj przełącznik **Raw features** / **Engineered features** i porównaj oba widoki.

   > **Dlaczego warto zobaczyć oba**: widok **Engineered features** mówi, na czym model liczy naprawdę - to jest odpowiedź techniczna. Widok **Raw features** przelicza ten wkład z powrotem na oryginalne kolumny danych i jest jedynym, który da się pokazać komuś spoza zespołu. Lekarzowi nic nie powie nazwa `BMI_MeanImputer`; zainteresuje go, ile znaczy sam wskaźnik BMI.

> **Uwaga**: Jeśli zamierzasz od razu przejść do [kolejnego ćwiczenia](Lab09B.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.

## Co dalej

Tutaj wyjaśnienia powstały same, bo tak działa AutoML z jednym włączonym ustawieniem. Model napisany własnoręcznie takiej wygody nie ma - trzeba mu wyjaśnienia dorobić. W [Lab 9B](Lab09B.md) zrobisz to na dwa sposoby: lokalnie, biblioteką uruchamianą wprost w notatniku, oraz jako **Responsible AI dashboard** zbudowany dla modelu zarejestrowanego w obszarze roboczym.
