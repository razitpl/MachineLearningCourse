# Lab 8B: Automatyczne uczenie maszynowe

## Po co jest to ćwiczenie?

Przy każdym nowym zbiorze danych wracają te same pytania: który algorytm sprawdzi się najlepiej? Czy cechy trzeba znormalizować? Czy zakodować inaczej? Odpowiedzi nie da się wyczytać z danych samym patrzeniem - trzeba sprawdzić empirycznie. Ręcznie oznacza to dziesiątki wariantów: dla każdego algorytmu inny zestaw przekształceń, dla każdego połączenia osobne trenowanie i osobne porównanie wyników.

**AutoML** (automatyczne uczenie maszynowe) automatyzuje właśnie ten przegląd. Podajesz dane, wskazujesz kolumnę do przewidzenia i metrykę, którą chcesz maksymalizować, a Azure Machine Learning sam generuje kolejne próby - każda z innym algorytmem i innym sposobem przygotowania danych - uruchamia je równolegle na klastrze i zestawia wyniki w jednym rankingu.

Warto od razu ustawić to we właściwym świetle. AutoML nie zastępuje zrozumienia problemu: nie powie, czy dane są wiarygodne, czy metryka odpowiada celowi biznesowemu ani czy przypadkiem nie podano modelowi kolumny, której w chwili przewidywania nie będzie. Daje natomiast solidny punkt odniesienia w godzinę zamiast w tydzień - i pokazuje, ile jeszcze da się wycisnąć ze starannie zbudowanego modelu własnego.

> **Czym to się różni od ćwiczenia 8A**: zadanie przeglądu (sweep) stroi wartości parametrów **jednego, wybranego przez Ciebie** algorytmu. AutoML wybiera także sam algorytm i przekształcenia danych. To ten sam pomysł - przeszukiwanie wariantów - zastosowany o poziom wyżej.

## Czego się nauczysz

1. Czym jest AutoML i jakie decyzje podejmuje za Ciebie, a jakich nie.
2. Jak przygotować dane wejściowe dla zadania AutoML (zasób typu `mltable`).
3. Jak skonfigurować zadanie klasyfikacji: kolumna docelowa, metryka główna, limity czasu i liczby prób.
4. Jak porównać próby i odnaleźć tę, która dała najlepszy model.
5. Jak obejrzeć kroki przygotowania danych wbudowane w zwycięski model i jak go zarejestrować.

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz pozostałe zasoby używane w tym ćwiczeniu.

## Krok 1: Uruchomienie automatycznego uczenia maszynowego

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.

2. Gdy instancja obliczeniowa jest uruchomiona, kliknij przy niej link **JupyterLab**, aby otworzyć JupyterLab w nowej karcie przeglądarki.

3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md), a w nim notatnik **08B - Using Automated Machine Learning.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a następnie czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

   > **Dlaczego kernel jest ważny**: kernel decyduje, które środowisko Pythona wykonuje kod. Tylko kernel z SDK v2 ma zainstalowany pakiet `azure-ai-ml`.

## Krok 2: Na co zwrócić uwagę w notatniku

### Przygotowanie danych

Notatnik łączy `data/diabetes.csv` i `data/diabetes2.csv` w jedną tabelę MLTable i rejestruje ją jako zasób danych `diabetes_mltable`.

> **Dlaczego to robimy**: zadanie AutoML przyjmuje na wejściu zasób danych tego typu - nie ścieżkę do pliku CSV. To dlatego przy tworzeniu zasobu danych w Studio wybiera się typ **Tabular**. Wybór z wcześniejszego ćwiczenia daje o sobie znać właśnie tutaj.

> **Dlaczego nie dzielimy danych na treningowe i testowe**: AutoML robi to sam - wydziela zbiór walidacyjny albo stosuje walidację krzyżową. Dzięki temu wszystkie próby są oceniane w identyczny sposób, co jest warunkiem uczciwego porównania.

### Klaster obliczeniowy

> **Dlaczego to musi być klaster**: AutoML opiera się na sprawdzaniu wielu wariantów. Na jednej maszynie próby wykonywałyby się jedna po drugiej i przegląd trwałby wielokrotnie dłużej. Klaster z kilkoma węzłami pozwala uruchamiać je równolegle.

### Konfiguracja zadania

Funkcja `automl.classification()` buduje zadanie, a `set_limits()` ustawia jego granice. Kluczowe parametry:

| Parametr | Co znaczy |
|---|---|
| `target_column_name="Diabetic"` | kolumna, którą model ma przewidywać; reszta kolumn to cechy |
| `primary_metric="AUC_weighted"` | metryka rozstrzygająca ranking prób |
| `featurization="auto"` | AutoML sam dobiera przekształcenia danych |
| `max_trials` / `max_concurrent_trials` | ile prób łącznie i ile naraz |
| `timeout_minutes` / `trial_timeout_minutes` | limit czasu dla całego zadania i dla pojedynczej próby |

> **Dlaczego metryka to nie szczegół techniczny**: to jedyna informacja o tym, co uznajesz za „lepszy model". W zbiorze `diabetes.csv` klasy są nierówne - około 66,6% do 33,4% - więc model przewidujący zawsze klasę większościową osiągnąłby 66,6% skuteczności, nie ucząc się zupełnie niczego. Skuteczność jako metryka główna premiowałaby takie modele. `AUC_weighted` mierzy zdolność odróżniania klas i na nierównowagę jest odporny, dlatego nadaje się tu znacznie lepiej.

> **Dlaczego limity czasu są obowiązkowe**: przeszukiwanie mogłoby trwać dowolnie długo, a każda minuta na klastrze kosztuje. Limity mówią wprost, ile czasu i pieniędzy wolno przeznaczyć na szukanie. W tym ćwiczeniu są celowo małe - w realnym projekcie przegląd bywa dłuższy, a liczba prób idzie w dziesiątki.

> **Na co uważać przy kolumnach wejściowych**: AutoML traktuje jako cechy wszystkie kolumny poza wskazaną kolumną docelową - także `PatientID`, który jest przecież tylko identyfikatorem i nie niesie żadnej informacji medycznej. W realnym projekcie taką kolumnę wyklucza się z danych wejściowych, bo podanie jej modelowi to klasyczny przeciek danych: model może „nauczyć się" numerów zamiast zależności i zawieść na nowych pacjentach. AutoML nie zna sensu kolumn, więc czuwanie nad tym, co trafia na wejście, pozostaje po Twojej stronie.

### Uruchomienie i obserwacja

> **Dlaczego to trwa**: najpierw klaster musi się uruchomić (i ewentualnie zwolnić węzły po poprzednim zadaniu), potem AutoML analizuje dane, a dopiero na końcu wykonują się kolejne próby. Postęp widać w logach w notatniku, a także w Azure Machine Learning studio - warto zajrzeć tam na karty **Models** i **Child jobs**, gdzie każda próba jest opisana użytym algorytmem i uzyskanymi metrykami.

### Wybór najlepszego modelu

Notatnik wypisuje wszystkie próby potomne, a następnie odczytuje z etykiety (taga) zadania nadrzędnego identyfikator najlepszej z nich.

> **Dlaczego warto obejrzeć całą listę**: jeśli różnica między pierwszym a dziesiątym wynikiem jest minimalna, wybór zwycięzcy zależy w dużej mierze od przypadku - a wtedy rozsądniej postawić na model prostszy albo szybszy w działaniu. Jeśli czołówka wyraźnie odstaje, wiadomo, że rodzaj algorytmu naprawdę ma tu znaczenie.

### Kroki przekształceń wewnątrz modelu

Notatnik wczytuje zwycięski model i wypisuje jego kroki (`named_steps`).

> **Dlaczego to ciekawe**: AutoML nie zwraca samego algorytmu, tylko potok przekształceń scikit-learn - przygotowanie danych i model w jednym obiekcie. Dzięki temu wdrożony model sam wykonuje na przychodzących danych dokładnie te same przekształcenia co przy trenowaniu, co eliminuje najczęstsze źródło błędów przy wdrożeniach. Ten wydruk pokazuje też, na czym polegała automatyczna obróbka cech - AutoML przestaje być czarną skrzynką.

> **Uwaga o nazewnictwie**: „potok" oznacza tutaj potok przekształceń scikit-learn wewnątrz modelu, a nie potok (pipeline) Azure Machine Learning znany z ćwiczeń 6A i 6B. To dwa różne pojęcia o tej samej nazwie.

### Rejestracja modelu

Ostatnia komórka rejestruje zwycięski model pod nazwą `diabetes_model_automl`.

> **Dlaczego pod inną nazwą**: model z AutoML powstał inną drogą niż modele z wcześniejszych ćwiczeń, a osobna nazwa pozwala trzymać oba w rejestrze i porównywać. Od tej chwili nic go nie odróżnia od modelu napisanego ręcznie - można go wdrożyć dokładnie tak samo jak w [Lab 7A](Lab07A.md).

> **Uwaga**: Jeśli zamierzasz od razu przejść do [kolejnego ćwiczenia](Lab09A.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.

## Co dalej

Masz model, o którym wiesz, że wygrał ranking - ale nie wiesz, na czym opiera swoje decyzje. Przy przewidywaniu cukrzycy to pytanie nie jest akademickie: lekarz zapyta, dlaczego akurat ten pacjent został wskazany. W [Lab 9A](Lab09A.md) zajrzysz do wyjaśnień, które AutoML generuje dla swoich modeli, i zobaczysz, które cechy najmocniej wpływają na wynik.
