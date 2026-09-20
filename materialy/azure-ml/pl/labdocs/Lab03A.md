# Lab 3A: Uruchamianie eksperymentów

## Po co jest to ćwiczenie?

Do tej pory kod uruchamiał się w notatniku - lokalnie, na instancji obliczeniowej. To wygodne przy eksploracji, ale ma trzy wady, które zaczynają boleć, gdy praca robi się poważna:

- **Nic się nie zapisuje.** Zamykasz notatnik i wiedza o tym, jaki wynik dał eksperyment i z jakimi parametrami, znika.
- **Nie da się tego powtórzyć.** Za tydzień nie odtworzysz, która wersja kodu dała accuracy 0.89.
- **Nie da się tego skalować.** Notatnik działa na jednej maszynie, za którą płacisz przez cały czas, gdy jest włączona.

Azure Machine Learning rozwiązuje to pojęciem **zadania (job)**: pakujesz skrypt, jego dane wejściowe i środowisko, a Azure ML uruchamia go za Ciebie, zapisując metryki, logi i pliki wyjściowe w historii obszaru roboczego. To ćwiczenie pokazuje ten mechanizm od podstaw - w kolejnych ćwiczeniach wszystko (trenowanie, strojenie hiperparametrów, potoki, AutoML) będzie już tylko wariantem tego samego pojęcia.

> **Jak to działa**: metryki zapisujesz przez **MLflow** - otwarty standard śledzenia eksperymentów. Azure ML rozumie go natywnie, dzięki czemu ten sam skrypt treningowy działa bez zmian zarówno lokalnie, jak i w chmurze.

## Czego się nauczysz

1. Jak uruchomić kod jako śledzone zadanie zamiast „zwykłego” kodu w notatniku.
2. Jak logować metryki i pliki wyjściowe, żeby dało się je później porównać.
3. Jak przeglądać historię zadań - w kodzie i w interfejsie Azure ML studio.

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz pozostałe zasoby używane tutaj.

## Krok 1: Uruchamianie eksperymentów na instancji obliczeniowej

Instancja obliczeniowa Azure Machine Learning to wygodne miejsce do uruchamiania kodu eksperymentów - działa bezpośrednio w Twoim obszarze roboczym.

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego; a na karcie **Compute Instances** upewnij się, że Twoja instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.

   > **Dlaczego to robimy**: instancja obliczeniowa to Twoja osobista maszyna deweloperska w chmurze. Notatnik musi mieć na czym działać - i jest to ta sama maszyna, na której później będziesz *zlecać* zadania do mocniejszego klastra. Pamiętaj: instancja kosztuje, dopóki jest uruchomiona, dlatego na końcu ćwiczenia ją zatrzymujesz.

2. Gdy instancja obliczeniowa jest uruchomiona, kliknij link **JupyterLab**, aby otworzyć go w nowej karcie przeglądarki.

   > **Dlaczego to robimy**: JupyterLab działa *na* instancji obliczeniowej, więc ma już zainstalowane SDK, dostęp do obszaru roboczego i tożsamość do uwierzytelnienia. Dzięki temu nie musisz konfigurować niczego lokalnie ani wpisywać haseł w kodzie.

3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md) (`~/cloudfiles/code/Users/<twoja-nazwa-uzytkownika>/MachineLearningCourse/materialy/azure-ml/pl`), a następnie otwórz notatnik **03A - Running Experiments.ipynb**. Upewnij się, że używa on kernela **Python 3.10 - SDK v2**, a następnie przeczytaj notatki w notatniku, uruchamiając kolejno każdą komórkę kodu.

   > **Dlaczego kernel jest ważny**: kernel określa, które środowisko Pythona wykonuje kod. Tylko kernel z SDK v2 ma zainstalowany pakiet `azure-ai-ml` - w innym dostaniesz `ModuleNotFoundError` przy pierwszym imporcie.

### Na co zwrócić uwagę w notatniku

Notatnik przeprowadza Cię przez trzy warianty tego samego pomysłu - warto rozumieć, czym się różnią:

| Co robisz | Po co |
|---|---|
| Logowanie metryk przez MLflow bezpośrednio w notatniku | Pokazuje, że śledzenie eksperymentu to osobna rzecz od tego, *gdzie* kod działa. Nawet kod w notatniku może trafić do historii obszaru roboczego. |
| Uruchomienie skryptu `.py` jako zadanie (`command`) | To jest docelowy sposób pracy: kod w pliku, który da się wersjonować w Git, uruchamiany w kontrolowanym środowisku. |
| Przeglądanie historii zadań | Sedno sprawy - wartość śledzenia widać dopiero wtedy, gdy masz za sobą kilka uruchomień i możesz porównać, który zestaw parametrów wypadł lepiej. |

> **Uwaga**: Jeśli zamierzasz przejść od razu do [kolejnego ćwiczenia](Lab03B.md), pozostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i **zatrzymać** (Stop) instancję obliczeniową, aby uniknąć niepotrzebnych kosztów.

## Co dalej

W [Lab 3B](Lab03B.md) użyjesz dokładnie tego samego mechanizmu zadań, ale z konkretnym celem: wytrenujesz model, zapiszesz go i **zarejestrujesz** w obszarze roboczym, żeby dało się go później wdrożyć. Zadanie z tego ćwiczenia jest więc cegiełką, na której opiera się cała reszta kursu.
