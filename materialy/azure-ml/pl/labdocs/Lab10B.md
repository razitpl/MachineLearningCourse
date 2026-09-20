# Lab 10B: Monitorowanie dryfu danych

## Po co jest to ćwiczenie?

Model uczy się na zdjęciu świata z konkretnego momentu. Świat nie stoi w miejscu: zmienia się średnia wieku pacjentów, zmieniają się normy laboratoryjne, w połowie roku przychodnia podłącza nowy analizator i wyniki insuliny zaczynają przychodzić w innej skali. Model tego nie zauważy. Będzie dalej zwracał przewidywania - równie szybko i równie pewnie - tyle że coraz gorsze.

To zjawisko nazywa się **dryfem danych (ang. *data drift*)**: dane napływające do wdrożonego modelu stopniowo przestają przypominać dane, na których był trenowany. Dryf jest podstępny, bo nie daje żadnego sygnału awarii. Nic się nie psuje, nic nie zwraca błędu - po prostu skuteczność cicho spada, a zauważa się to zwykle wtedy, gdy ktoś z zewnątrz zgłosi, że wyniki przestały się zgadzać.

**Monitorowanie modelu** w Azure Machine Learning automatyzuje wykrywanie tego zjawiska: cyklicznie porównuje prawdziwy ruch produkcyjny z danymi odniesienia i wylicza miarę rozjazdu - osobno dla każdej cechy i zbiorczo dla całego modelu. Gdy miara przekroczy ustalony próg, wysyła powiadomienie.

> **Czego to wymaga**: monitorowania dryfu nie da się zrobić na dwóch plikach CSV porównanych w notatniku. Mechanizm opiera się na **danych produkcyjnych** zbieranych automatycznie przez *data collector* działający na wdrożonym punkcie końcowym (endpoint), więc potrzebne są trzy rzeczy naraz: model wdrożony do zarządzanego punktu końcowego z włączonym zbieraniem danych, jakiś ruch produkcyjny, który ten kolektor zdąży zebrać, oraz **harmonogram monitorowania**, czyli cykliczne zadanie (job) wykonujące porównanie. To ćwiczenie składa wszystkie trzy elementy.

## Czego się nauczysz

1. Czym jest dryf danych i dlaczego nie objawia się jako awaria usługi.
2. Skąd biorą się dane produkcyjne do porównania i dlaczego muszą pochodzić z wdrożonego punktu końcowego.
3. Czym są **dane odniesienia (ang. *reference data*)** i dlaczego zwykle jest nimi zbiór treningowy.
4. Jak zdefiniować **sygnał dryfu danych** wraz z progami dla cech liczbowych i kategorialnych.
5. Jak utworzyć harmonogram monitorowania i przypiąć do niego powiadomienia e-mail.
6. Jak odczytać wyniki monitora w Studio i wyłączyć harmonogram, gdy przestaje być potrzebny.

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz zasób danych `diabetes_mltable` używany tu jako dane odniesienia.

Konieczne są też dwa wcześniejsze ćwiczenia:

- [Lab 7A](Lab07A.md) - wdraża model `diabetes_model` do zarządzanego punktu końcowego `diabetes-endpoint` (wdrożenie `blue`).
- [Lab 10A](Lab10A.md) - włącza dla tego samego wdrożenia `blue` diagnostykę Application Insights oraz **zbieranie danych produkcyjnych**.

> **Dlaczego [Lab 10A](Lab10A.md) jest tu obowiązkowy**: to właśnie zbieranie danych włączone w tamtym ćwiczeniu dostarcza dane produkcyjne, które analizuje monitor. Bez niego harmonogram powstanie i będzie się uruchamiał, ale każde uruchomienie zakończy się bez wyniku, bo nie znajdzie żadnego ruchu do porównania. To najczęstsza przyczyna pustego pulpitu monitorowania.

Jeśli używasz innych nazw punktu końcowego, wdrożenia lub zasobu danych, podmień je w kodzie notatnika.

## Krok 1: Konfiguracja monitorowania dryfu danych

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.

2. Gdy instancja obliczeniowa jest uruchomiona, kliknij przy niej link **JupyterLab**, aby otworzyć JupyterLab w nowej karcie przeglądarki.

3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md), a w nim notatnik **10B - Monitoring Data Drift.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a następnie czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

   > **Dlaczego kernel jest ważny**: kernel decyduje, które środowisko Pythona wykonuje kod. Tylko kernel z SDK v2 ma zainstalowany pakiet `azure-ai-ml`.

## Krok 2: Na co zwrócić uwagę w notatniku

### Symulacja ruchu produkcyjnego

Notatnik wczytuje plik `data/diabetes2.csv`, celowo przesuwa wartości kilku cech i wysyła powstałe dane jako zapytania do punktu końcowego `diabetes-endpoint`.

> **Dlaczego dane trzeba wysłać przez punkt końcowy, a nie wczytać z pliku**: monitor nie potrafi czytać plików z dysku instancji obliczeniowej. Analizuje wyłącznie to, co kolektor zapisał podczas obsługi prawdziwych zapytań. Jedyna droga, żeby dane stały się danymi produkcyjnymi, prowadzi przez wywołanie wdrożonej usługi - i właśnie to robi ta komórka.

> **Dlaczego wartości są przesunięte**: świeżo wdrożona usługa dostaje ruch bardzo podobny do zbioru treningowego, więc dryf byłby zerowy i nie byłoby czego oglądać. Sztuczne przesunięcie kilku cech odtwarza w kilka minut sytuację, która w rzeczywistości narastałaby miesiącami.

> **Uwaga o czasie i ilości danych**: zebrane dane pojawiają się w magazynie obszaru roboczego z kilkuminutowym opóźnieniem, a monitorowanie potrzebuje sensownej ilości ruchu - zwykle gromadzonego przez co najmniej kilka uruchomień harmonogramu - zanim wynik zacznie coś znaczyć. Ta komórka ma zapewnić, że w rurze w ogóle są jakieś dane, a nie wytworzyć statystycznie wiarygodny obraz dryfu.

### Dane odniesienia

Jako dane odniesienia monitor dostaje zarejestrowany zasób `diabetes_mltable`.

> **Dlaczego akurat zbiór treningowy**: pytanie brzmi „czy dzisiejsze dane przypominają jeszcze te, na których model się uczył". Punktem odniesienia musi więc być dokładnie to, co model widział podczas trenowania. Można też porównywać z ruchem z wybranego okresu w przeszłości - wtedy odpowiadasz na inne pytanie: „czy coś zmieniło się od zeszłego miesiąca".

### Sygnał dryfu i progi

Kod definiuje `DataDriftSignal` z progami osobno dla cech liczbowych i kategorialnych, po czym opakowuje go w `MonitorDefinition`.

> **Co robi próg**: dla każdej cechy liczona jest miara odległości między rozkładem produkcyjnym a rozkładem odniesienia. Sama liczba niczego nie rozstrzyga - dopiero próg zamienia ją w decyzję „to jeszcze normalne wahanie" albo „to już dryf wymagający reakcji". Próg dobiera się do konkretnego zastosowania: przy modelu medycznym opłaca się być przewrażliwionym, przy modelu rekomendującym filmy - raczej nie.

> **Dlaczego liczbowe i kategorialne osobno**: dla kolumny liczbowej porównuje się kształt rozkładu wartości, dla kolumny z kategoriami - udziały poszczególnych kategorii. To różne miary o różnych skalach, więc mają osobne progi.

### Harmonogram i powiadomienia

`MonitorSchedule` uruchamia monitor cyklicznie na obliczeniach typu serverless Spark, a `AlertNotification` wskazuje adresy e-mail, na które trafią ostrzeżenia.

> **Dlaczego harmonogram, a nie jednorazowe uruchomienie**: dryf jest z definicji zjawiskiem rozłożonym w czasie. Pojedynczy pomiar mówi tylko tyle, ile pojedynczy pomiar temperatury - dopiero seria pokazuje trend i pozwala stwierdzić, kiedy przekroczono granicę. Harmonogram jest więc częścią mechanizmu, a nie wygodą.

> **Dlaczego obliczenia są bezserwerowe**: monitor uruchamia się rzadko i na krótko. Utrzymywanie dla niego własnego klastra oznaczałoby płacenie za maszynę, która przez większość doby nic nie robi. Serverless Spark powstaje na czas przebiegu i znika po nim.

> **Uwaga o adresie e-mail**: w notatniku adres powiadomień jest ustawiony na właściciela obszaru roboczego - zmień go, jeśli ostrzeżenia mają trafiać gdzie indziej. W materiałach przykładowych używaj placeholdera w rodzaju `you@example.com`, a nie prawdziwego adresu.

## Krok 3: Obejrzenie wyników monitorowania

1. W [Azure Machine Learning studio](https://ml.azure.com) wybierz **Manage** > **Monitoring**.

2. Wybierz harmonogram `diabetes-model-monitor` utworzony w notatniku.

3. Gdy harmonogram wykona się co najmniej raz, przejrzyj sygnał **data drift** - zbiorczą miarę dryfu oraz wkład poszczególnych cech.

   > **Dlaczego wkład cech jest ważniejszy niż wynik zbiorczy**: liczba zbiorcza mówi tylko, że coś się zmieniło. Rozbicie na cechy mówi, co się zmieniło - a to często wskazuje wprost na przyczynę. Dryf w jednej kolumnie z wynikiem laboratoryjnym sugeruje zmianę aparatury albo jednostek. Dryf w wieku i wadze naraz sugeruje, że zmienił się sam zbiór pacjentów. Reakcja na każdy z tych przypadków jest inna.

   > **Co zrobić po wykryciu dryfu**: samo wykrycie nie naprawia modelu. Typowa odpowiedź to wytrenowanie go ponownie na świeższych danych - w praktyce oznacza to uruchomienie potoku z [Lab 6A](Lab06A.md) na nowym zbiorze i wdrożenie nowej wersji jak w [Lab 7A](Lab07A.md). Czasem jednak właściwą reakcją jest naprawa danych u źródła, bo dryf okazuje się usterką w procesie ich zbierania.

   > **Uwaga o czekaniu**: monitor działa według harmonogramu zdefiniowanego w notatniku, więc wyniki nie pojawią się od razu. Nie trzeba czekać na kolejny cykl - przebieg da się uruchomić na żądanie ze strony harmonogramu w Studio.

4. Jeśli któraś miara przekroczy swój próg, wszystkie osoby z listy powiadomień dostaną wiadomość e-mail, a przebieg zostanie oznaczony w historii monitora.

## Krok 4: Wyłączenie harmonogramu

Ostatnia komórka notatnika wyłącza harmonogram `diabetes-model-monitor`.

> **Dlaczego to robimy**: harmonogram, o którym się zapomni, uruchamia się dalej i codziennie zużywa obliczenia, za które płacisz. Wyłączenie zatrzymuje kolejne przebiegi, ale zachowuje definicję monitora i jego historię - można go włączyć ponownie. Jeśli monitor nie będzie już potrzebny, w notatniku jest też zakomentowana linia usuwająca go całkowicie.

> **Uwaga na koniec kursu**: jeśli kończysz pracę z ćwiczeniami, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów. Jeśli obszar roboczy nie będzie już potrzebny, usuń w subskrypcji Azure całą grupę zasobów, w której go utworzono - w niej leżą również konto magazynu, rejestr kontenerów i Application Insights, które kosztują niezależnie od tego, czy z nich korzystasz.

## Podsumowanie całej ścieżki

To ostatnie ćwiczenie kursu. Warto spojrzeć na drogę, którą przeszedł model cukrzycowy, bo jest to pełny cykl życia rozwiązania uczenia maszynowego:

| Etap | Ćwiczenia | Co się dokonało |
|---|---|---|
| Środowisko pracy | [1A](Lab01A.md), [1B](Lab01B.md), [2A](Lab02A.md), [2B](Lab02B.md) | obszar roboczy, instancja obliczeniowa, pierwsze modele bez pisania kodu |
| Eksperymenty i trenowanie | [3A](Lab03A.md), [3B](Lab03B.md) | kod uruchamiany jako śledzone zadanie, metryki w MLflow, rejestracja modelu |
| Dane i środowiska | [4A](Lab04A.md), [4B](Lab04B.md), [5A](Lab05A.md), [5B](Lab05B.md) | magazyny danych, zasoby danych, powtarzalne środowiska, klastry obliczeniowe |
| Automatyzacja | [6A](Lab06A.md), [6B](Lab06B.md) | potoki składane z komponentów, publikowane i wyzwalane na żądanie |
| Udostępnianie modelu | [7A](Lab07A.md), [7B](Lab07B.md) | wnioskowanie w czasie rzeczywistym i wsadowe |
| Poprawa jakości | [8A](Lab08A.md), [8B](Lab08B.md) | strojenie hiperparametrów, automatyczne uczenie maszynowe |
| Zrozumienie modelu | [9A](Lab09A.md), [9B](Lab09B.md) | ważność cech, pulpit Responsible AI |
| Utrzymanie w produkcji | [10A](Lab10A.md), 10B (to ćwiczenie) | telemetria, dane produkcyjne, wykrywanie dryfu danych |

Najważniejsza rzecz do zabrania z tego kursu jest taka: wytrenowanie modelu jest najmniejszą częścią pracy. Wszystko wokół - dane, powtarzalność, wdrożenie, wyjaśnialność, monitorowanie - decyduje o tym, czy model przyniesie komukolwiek pożytek. I cykl się zamyka: dryf wykryty w tym ćwiczeniu prowadzi wprost do ponownego trenowania, czyli z powrotem na początek ścieżki - tyle że tym razem wszystko jest już zautomatyzowane.
