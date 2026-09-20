# Lab 1A: Tworzenie obszaru roboczego Azure Machine Learning

## Po co jest to ćwiczenie?

Zanim wytrenujesz jakikolwiek model, potrzebujesz miejsca, w którym będzie mieszkać cała praca: dane, moc obliczeniowa, historia eksperymentów, gotowe modele i ich wdrożenia. W Azure Machine Learning tym miejscem jest **obszar roboczy (workspace)**.

Bez niego każdy element żyłby osobno: dane na jakimś dysku, model na czyimś laptopie, wyniki eksperymentów w notatkach. Obszar roboczy spina to w jedną, wersjonowaną całość, do której ma dostęp cały zespół - i to on jest punktem odniesienia dla każdego kolejnego ćwiczenia w tym kursie.

W tym ćwiczeniu zbudujesz fundament, z którego będziesz korzystać do końca kursu: obszar roboczy, dwa rodzaje zasobów obliczeniowych i pierwszy zasób danych.

## Czego się nauczysz

1. Czym jest obszar roboczy Azure ML i jakie zasoby Azure powstają razem z nim.
2. Jaka jest różnica między **instancją obliczeniową** a **klastrem obliczeniowym** i kiedy używa się której.
3. Jak zarejestrować dane jako **zasób danych**, żeby dało się ich używać w zadaniach bez podawania ścieżek do plików.

## Zanim zaczniesz

Azure Machine Learning (Azure ML) to usługa Microsoft Azure do uruchamiania w chmurze, na dużą skalę, zadań z obszaru analizy danych i uczenia maszynowego. Potrzebujesz subskrypcji Azure - skorzystaj z subskrypcji studenckiej.

## Krok 1: Utwórz obszar roboczy Azure ML

Obszar roboczy to scentralizowane miejsce zarządzania wszystkimi zasobami Azure ML potrzebnymi w projekcie uczenia maszynowego.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i utwórz nowy zasób **Machine Learning**:
    * **Workspace name**: dowolna unikalna nazwa (np. `ml-kurs-<twoje-inicjaly>`)
    * **Resource group**: utwórz nową (np. `rg-ml-kurs`)
    * **Region**: wybierz region blisko siebie, np. **West Europe**

   > **Co to jest grupa zasobów**: to „teczka" na powiązane ze sobą zasoby Azure. Wszystko, co powstanie w tym kursie, wyląduje w jednej grupie - dzięki temu na koniec możesz usunąć jedną grupę i mieć pewność, że nic nie zostało i nic nie generuje kosztów.
   >
   > **Uwaga o regionie**: region decyduje, w którym fizycznie centrum danych stoją Twoje maszyny. Bliższy region to niższe opóźnienia. Jeśli przy tworzeniu maszyn w kolejnym kroku okaże się, że w Twojej subskrypcji brakuje limitu (*quota*) na wybrany rozmiar maszyny w tym regionie, załóż obszar roboczy w innym regionie.

   > **Dlaczego to robimy**: tworząc obszar roboczy, Azure zakłada w tle kilka powiązanych zasobów, których nie musisz konfigurować ręcznie:
   > - **konto magazynu (Storage account)** - tu trafiają Twoje notatniki, pliki danych i wyniki zadań,
   > - **Key Vault** - sejf na hasła i klucze dostępu, żeby nie trzymać ich w kodzie,
   > - **Application Insights** - zbiera telemetrię z wdrożonych modeli (użyjesz tego w Lab 10A),
   > - **Container Registry** - przechowuje obrazy kontenerów budowane przy definiowaniu środowisk (Lab 5A).
   >
   > Zobaczysz je wszystkie w grupie zasobów i to one - a nie sam obszar roboczy - generują większość kosztów.

2. Gdy obszar roboczy i powiązane z nim zasoby zostaną utworzone, wyświetl obszar roboczy w portalu.

## Krok 2: Poznaj interfejs Azure ML studio

Zasobami obszaru roboczego można zarządzać w portalu Azure, ale dla osoby pracującej z danymi portal jest zaśmiecony - pełno w nim ustawień dotyczących ogólnej administracji Azure. Dlatego Azure ML ma własny interfejs internetowy.

> **Uwaga**: Interfejs webowy Azure ML nazywa się *Azure Machine Learning studio*. Bywa to mylące, bo istniał też osobny, starszy produkt *Azure Machine Learning Studio* (classic) do budowania modeli wizualnym projektantem.

1. Na stronie obszaru roboczego w portalu Azure kliknij link uruchamiający **Azure Machine Learning studio**; alternatywnie otwórz w nowej karcie [https://ml.azure.com](https://ml.azure.com). W razie potrzeby zaloguj się kontem Microsoft użytym w poprzednim kroku i wybierz swoją subskrypcję oraz obszar roboczy.
2. Rozejrzyj się po interfejsie - stąd zarządzasz wszystkimi zasobami obszaru roboczego.

   > **Dlaczego to robimy**: przez resztę kursu będziesz się przełączać między kodem (SDK) a tym interfejsem. Wszystko, co zrobisz kodem, natychmiast widać w studio - i odwrotnie. Warto od początku wiedzieć, gdzie szukać: **Jobs** (historia zadań), **Data** (zasoby danych), **Compute**, **Models**, **Endpoints**.

## Krok 3: Utwórz zasoby obliczeniowe

Jedną z głównych zalet Azure ML jest to, że moc obliczeniowa jest „na żądanie" - płacisz za nią tylko wtedy, gdy faktycznie liczy.

Utworzysz teraz dwa różne rodzaje zasobów obliczeniowych, bo służą do zupełnie różnych celów:

| Zasób | Do czego służy | Jak się rozlicza |
|---|---|---|
| **Instancja obliczeniowa** (`mymachine`) | Twoja osobista maszyna deweloperska w chmurze - tu działa JupyterLab i z niej zlecasz zadania. | Kosztuje, dopóki jest **uruchomiona** - dlatego włącza się automatyczne wyłączanie. |
| **Klaster obliczeniowy** (`aml-cluster`) | Maszyny do wykonywania zadań treningowych; Azure włącza je na czas zadania i gasi po nim. | Kosztuje tylko w trakcie zadania; przy zerze węzłów minimalnych - nic. |

1. W Azure Machine Learning studio przejdź na stronę **Compute**. Tutaj zarządzasz wszystkimi środowiskami obliczeniowymi.
2. Na karcie **Compute instances** dodaj nową instancję obliczeniową z następującymi ustawieniami. Tej maszyny użyjesz do uruchomienia JupyterLab i notatników SDK v2 w kolejnym ćwiczeniu.
    * **Compute name**: mymachine
    * **Virtual Machine size**: Standard_D2as_v4
    * **Enable idle shutdown**: 30 minut (lub mniej)

   > **Dlaczego ustawiamy automatyczne wyłączanie**: to najczęstsza przyczyna niespodziewanych rachunków na kursach - zapomniana, działająca przez weekend instancja. Automatyczne wyłączanie po okresie bezczynności to zabezpieczenie przed tym.

3. Kiedy instancja się tworzy, przejdź na kartę **Compute clusters** i dodaj nowy klaster z następującymi ustawieniami:
    * **Compute name**: aml-cluster
    * **Virtual Machine size**: Standard_D2as_v4
    * **Minimum number of nodes**: 0
    * **Maximum number of nodes**: zależnie od limitu (quota) - zwykle wystarczą 2-3 węzły
    * **Idle seconds before scale down**: 300

   > **Co to jest węzeł (node)**: pojedyncza maszyna wirtualna w klastrze. Klaster o maksymalnie 3 węzłach może wykonywać do trzech zadań (albo trzech prób tego samego zadania) równolegle.
   >
   > **Dlaczego minimum wynosi 0**: przy zerze klaster po zakończeniu zadania gasi wszystkie węzły i przestaje generować koszt. Ceną za to jest kilka minut oczekiwania na starcie kolejnego zadania (węzeł musi się dopiero podnieść) - w warunkach kursu to bardzo dobry kompromis.
   >
   > **Dlaczego 300 sekund, a nie mniej**: to czas, przez jaki klaster czeka *po* zakończeniu zadania, zanim wyłączy węzły. Gdyby ustawić np. 30 sekund, węzeł gasłby niemal natychmiast i **każde** kolejne zadanie zaczynałoby się od kilkuminutowego rozruchu maszyny. Przy 300 sekundach (5 minut) zadania uruchamiane jedno po drugim - a tak właśnie pracujesz w trakcie ćwiczenia - trafiają na wciąż działający węzeł i ruszają od razu. Po skończonej pracy klaster i tak się wygasi, więc nie płacisz za bezczynność dłużej niż te 5 minut.
   >
   > **Co oznacza `Standard_D2as_v4`**: to nazwa rozmiaru maszyny wirtualnej w Azure - w tym przypadku 2 rdzenie procesora i 8 GB pamięci RAM. Do danych o wielkości kilku megabajtów, jakich używamy w kursie, to w zupełności wystarczy. Większe maszyny (i te z kartą GPU) kosztują odpowiednio więcej.

## Krok 4: Utwórz zasób danych

Masz już na czym liczyć - teraz potrzebujesz danych, na których będziesz liczyć.

1. W studio przejdź na stronę **Data**. Twój obszar roboczy ma już kilka **magazynów danych (datastores)** opartych na koncie Azure Storage utworzonym razem z obszarem roboczym. Służą one do przechowywania notatników, plików konfiguracyjnych i danych.

   > **Dlaczego magazyn danych to nie to samo co zasób danych**: magazyn danych (datastore) to *połączenie* do miejsca, gdzie leżą dane - wraz z uprawnieniami. Zasób danych (data asset) to *konkretne dane* pod nazwą i wersją. Dzięki temu w kodzie piszesz `diabetes_dataset` zamiast pełnej ścieżki i klucza dostępu do storage.
   >
   > W prawdziwym projekcie dodaje się własne magazyny wskazujące na firmowe źródła - kontenery Azure Blob, Azure Data Lake, bazy Azure SQL. Wrócisz do tego w dalszej części kursu.

2. Przejdź na kartę **Data assets**. Zasoby danych reprezentują konkretne pliki lub tabele, z których będziesz korzystać w Azure ML.
3. Pobierz na swój komputer plik [diabetes.csv](https://raw.githubusercontent.com/razitpl/MachineLearningCourse/master/materialy/azure-ml/pl/data/diabetes.csv) - w kolejnym kroku go wgrasz.
4. Utwórz nowy zasób danych z plików lokalnych, z następującymi ustawieniami:
    * **Name**: diabetes_dataset (*zwróć uwagę na wielkość liter i znak podkreślenia*)
    * **Dataset type**: Tabular
    * **Description**: Diabetes data
    * **Destination storage type**: Azure Blob Storage (prawdopodobnie workspaceblobstore)
    * **Settings and preview**: przejrzyj automatycznie wykryte ustawienia.
    * **Schema**: przejrzyj domyślnie wybrane kolumny i typy danych.

   > **Dlaczego typ „Tabular" ma znaczenie**: wybranie **Tabular** rejestruje dane jako typ `mltable` - czyli definicję *tabeli* (jak odczytać pliki, jaki separator, jakie kolumny), a nie sam plik. Tego typu wymagają później AutoML (Lab 8B), pulpit Responsible AI (Lab 9) i monitorowanie modelu (Lab 10). Gdyby wybrać typ plikowy, te ćwiczenia by nie zadziałały.

5. Po utworzeniu zasobu danych otwórz go i przejdź na kartę **Explore**, aby zobaczyć próbkę danych.

   > **Co jest w tych danych**: każdy wiersz to jeden pacjent. Osiem kolumn to **cechy** (ang. *features*), na podstawie których model będzie przewidywał: liczba ciąż (`Pregnancies`), poziom glukozy (`PlasmaGlucose`), ciśnienie rozkurczowe (`DiastolicBloodPressure`), grubość fałdu skórnego (`TricepsThickness`), poziom insuliny (`SerumInsulin`), BMI, wskaźnik obciążenia rodzinnego cukrzycą (`DiabetesPedigree`) i wiek (`Age`).
   >
   > Dziewiąta kolumna, `Diabetic`, to **etykieta** (ang. *label*) - wartość 0 lub 1 mówiąca, czy u pacjenta stwierdzono cukrzycę. To właśnie ją model będzie przewidywał. Takie zadanie - przewidywanie jednej z dwóch możliwych wartości - nazywamy **klasyfikacją binarną**.
   >
   > Z tych danych będziesz korzystać w większości kolejnych ćwiczeń, więc warto teraz przez chwilę na nie popatrzeć.

   > **Uwaga**: Możesz opcjonalnie wygenerować *profil* danych, żeby szybko ocenić ich strukturę i jakość: liczbę wierszy i kolumn, wykryte typy, braki, wartości unikalne i podstawowe statystyki kolumn liczbowych.
   >
   > Profilowanie niczego nie zmienia w danych ani nie trenuje modelu - pomaga tylko wcześnie wykryć problemy z jakością danych. Wrócisz do tego tematu w dalszej części kursu.

## Co dalej

W [Lab 1B](Lab01B.md) połączysz się z tym obszarem roboczym z poziomu kodu Pythona (SDK v2) i sprawdzisz, że widzisz z niego dokładnie te zasoby, które przed chwilą powstały z klikania w interfejsie.
