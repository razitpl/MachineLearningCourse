# Lab 7B: Tworzenie usługi wnioskowania wsadowego

## Po co jest to ćwiczenie?

Endpoint z poprzedniego ćwiczenia odpowiada na pojedyncze pytania od razu. Nie każdy problem tak wygląda.

Wyobraź sobie przychodnię, w której przez cały dzień przyjmowani są pacjenci, a wyniki pomiarów każdego z nich lądują w osobnym pliku. Nikt nie potrzebuje przewidywania w tej samej sekundzie - wystarczy, że w nocy model przetworzy wszystkie pliki z danego dnia, a rano personel zastanie gotową listę osób, które warto wezwać na badanie. Tak działa **wnioskowanie wsadowe** (ang. *batch inferencing*): duża paczka danych, przetwarzana naraz, wynik po jakimś czasie.

Różnica nie dotyczy tylko wygody. Endpoint czasu rzeczywistego musi trzymać włączoną maszynę bez przerwy, żeby móc odpowiedzieć w każdej chwili - i przez cały ten czas kosztuje. Punkt końcowy wsadowy uruchamia klaster tylko na czas przetwarzania i gasi go po zakończeniu. Przy danych, które i tak spływają raz na dobę, jest to rozwiązanie wielokrotnie tańsze. Dobór właściwego trybu wnioskowania do problemu jest więc decyzją architektoniczną, a nie kwestią gustu.

## Czego się nauczysz

1. Czym jest **punkt końcowy (endpoint) wsadowy** i kiedy wybrać go zamiast endpointu czasu rzeczywistego.
2. Jak przygotować i zarejestrować folder plików wejściowych jako zasób danych typu `uri_folder`.
3. Jak napisać skrypt scoringowy przetwarzający całą paczkę plików naraz.
4. Jak skonfigurować wdrożenie wsadowe: klaster, liczbę węzłów, rozmiar mini-paczki i sposób zapisu wyników.
5. Jak wywołać punkt końcowy wsadowy i pobrać plik z przewidywaniami.

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz pozostałe zasoby używane w tym ćwiczeniu. Warto też mieć za sobą [Lab 7A](Lab07A.md), gdzie omówione jest pojęcie skryptu scoringowego.

## Krok 1: Wdrożenie modelu jako punktu końcowego wsadowego

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.

2. Gdy instancja obliczeniowa jest uruchomiona, kliknij przy niej link **JupyterLab**, aby otworzyć JupyterLab w nowej karcie przeglądarki.

3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md), a w nim notatnik **07B - Creating a Batch Inferencing Service.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a następnie czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

   > **Dlaczego kernel jest ważny**: kernel decyduje, które środowisko Pythona wykonuje kod. Tylko kernel z SDK v2 ma zainstalowany pakiet `azure-ai-ml`.

## Krok 2: Na co zwrócić uwagę w notatniku

### Trenowanie i rejestracja modelu na początku

Podobnie jak w [Lab 7A](Lab07A.md), notatnik zaczyna od wytrenowania drzewa decyzyjnego i zarejestrowania go jako `diabetes_model`. **Tę komórkę uruchamiasz zawsze.**

> **Dlaczego to robimy**: wsadowy skrypt scoringowy wczytuje model funkcją `joblib.load` z pliku `diabetes_model.pkl`, czyli oczekuje zwykłego modelu scikit-learn zarejestrowanego jako zasób typu `CUSTOM_MODEL`. Wcześniejsze ćwiczenia rejestrują `diabetes_model` w formacie MLflow o innej strukturze. Ta komórka gwarantuje, że w obszarze roboczym jest wersja w formacie, którego skrypt się spodziewa.

### Wygenerowanie danych wejściowych

Notatnik losuje 100 obserwacji z pliku `data/diabetes2.csv` i zapisuje każdą z nich jako osobny plik CSV w folderze `batch-data`, a potem rejestruje ten folder jako zasób danych `diabetes_batch_data` typu `uri_folder`.

> **Dlaczego osobne pliki, a nie jedna tabela**: to symulacja realnej sytuacji - każdy plik odpowiada jednemu pacjentowi przyjętemu w ciągu dnia. Wnioskowanie wsadowe jest zaprojektowane właśnie pod taki wzorzec: „przetwórz wszystko, co leży w tym folderze". Zwróć uwagę, że pliki zawierają wyłącznie cechy, bez kolumny `Diabetic` - to dane nowych pacjentów, o których wynik dopiero pytamy.

> **Dlaczego rejestrujemy je jako zasób danych**: zarejestrowany zasób ma nazwę i wersję, więc zadanie przetwarzania odwołuje się do `diabetes_batch_data` zamiast do ścieżki na czyimś dysku. Za tydzień będzie wiadomo dokładnie, na jakich danych powstały te przewidywania.

### Klaster obliczeniowy

Notatnik odnajduje (albo tworzy) klaster `aml-cluster`.

> **Dlaczego klaster, a nie instancja obliczeniowa**: sto plików można rozdzielić między kilka maszyn i przetworzyć równolegle - na tym polega cała oszczędność czasu przy wnioskowaniu wsadowym. Klaster ma ustawione `min_instances=0`, więc między zadaniami gaśnie i nic nie kosztuje. Ceną jest kilka minut rozruchu przy starcie pierwszego zadania (job).

### Wsadowy skrypt scoringowy

`batch_diabetes.py` ma te same dwie funkcje co skrypt czasu rzeczywistego, ale `run()` dostaje coś innego:

| | Czas rzeczywisty (7A) | Wsadowo (7B) |
|---|---|---|
| Argument `run()` | JSON z jednego zapytania HTTP | lista ścieżek do plików (**mini-paczka**) |
| Wynik | odpowiedź odsyłana do klienta | lista wierszy dopisywana do wspólnego pliku |
| Kto wywołuje | aplikacja kliencka | wdrożenie wsadowe, w pętli po wszystkich plikach |

> **Dlaczego `run()` dostaje paczkę plików, a nie jeden plik**: uruchomienie funkcji ma swój narzut. Przy dziesięciu tysiącach plików wywoływanie jej osobno dla każdego zajęłoby więcej czasu niż same obliczenia. Ustawienie `mini_batch_size=5` mówi: „przekazuj po pięć plików naraz".

### Konfiguracja wdrożenia

Notatnik tworzy punkt końcowy `diabetes-batch-endpoint` z wdrożeniem `diabetes-batch-dpl`. Warto zrozumieć jego ustawienia:

| Ustawienie | Znaczenie |
|---|---|
| `instance_count=2` | ile węzłów klastra przetwarza dane równolegle |
| `max_concurrency_per_instance=2` | ile procesów scoringowych działa na jednym węźle |
| `mini_batch_size=5` | ile plików trafia do jednego wywołania `run()` |
| `output_action=APPEND_ROW` | wyniki ze wszystkich węzłów są doklejane do jednego pliku zbiorczego |
| `output_file_name="predictions.csv"` | nazwa tego pliku |

> **Dlaczego to jest sedno wnioskowania wsadowego**: nie trzeba samodzielnie pisać kodu, który dzieli pracę między maszyny, pilnuje kolejności i scala wyniki. Wystarczy opisać skalę przetwarzania kilkoma liczbami, a Azure ML zajmie się rozdzieleniem plików i zebraniem rezultatów.

### Uruchomienie i odbiór wyników

Wywołanie `invoke` zwraca zadanie, którego przebieg widać w notatniku. Po jego zakończeniu notatnik pobiera plik `predictions.csv` i wyświetla pierwsze wiersze.

> **Dlaczego wyniki pobiera się z zadania potomnego**: wywołanie punktu końcowego wsadowego tworzy zadanie nadrzędne, a faktyczne przetwarzanie odbywa się w zadaniu potomnym - to ono ma wyjście o nazwie `score`. Stąd w kodzie krok pośredni polegający na odnalezieniu zadania potomnego.

> **Dlaczego to może potrwać**: do czasu samego przetwarzania dochodzi rozruch węzłów klastra, który wcześniej był wygaszony. Przy takiej liczbie plików najwięcej czasu zajmuje właśnie start maszyn, a nie obliczenia.

### Korzystanie z endpointu z aplikacji

Gotowy punkt końcowy wsadowy jest trwałym zasobem, który można wywołać przez SDK, przez Azure CLI (`az ml batch-endpoint invoke`) albo przez REST API - na przykład z harmonogramu uruchamianego co noc.

> **Różnica wobec ćwiczenia 7A**: punkty końcowe wsadowe uwierzytelniają wywołania REST tokenem Microsoft Entra ID, a nie prostym kluczem. Aplikacja działająca automatycznie logowałaby się więc jako jednostka usługi (*service principal*) i przedstawiała token.

### Sprzątanie

> **Dlaczego tu nie ma pośpiechu**: wdrożenie wsadowe zużywa moc obliczeniową wyłącznie wtedy, gdy działa zadanie przetwarzania. Sam pozostawiony punkt końcowy nie generuje kosztów - w przeciwieństwie do endpointu czasu rzeczywistego z [Lab 7A](Lab07A.md), który trzeba usunąć. Ostatnia komórka pozwala go mimo wszystko usunąć, jeśli chcesz uporządkować obszar roboczy.

> **Uwaga**: Jeśli zamierzasz od razu przejść do [kolejnego ćwiczenia](Lab08A.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.

## Co dalej

Masz już model wytrenowany, wdrożony i używany w obu trybach. W [Lab 8A](Lab08A.md) wrócisz o krok wstecz, do samego trenowania, i zajmiesz się pytaniem, które dotąd pomijaliśmy milczeniem: skąd wiadomo, że wartości parametrów użyte przy trenowaniu były dobre? Zamiast zgadywać, pozwolisz Azure ML przetestować wiele wariantów równolegle.
