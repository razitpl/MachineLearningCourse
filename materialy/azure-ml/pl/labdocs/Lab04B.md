# Lab 4B: Praca z zestawami danych (Datasets)

Zasoby danych (data assets) pozwalają opakować dane na potrzeby zadań (jobs) i trenowania. Zasoby tabelaryczne (`mltable`) i plikowe (`uri_folder`) służą do definiowania wersjonowanych źródeł danych, które łatwo wykorzystasz w kolejnych zadaniach.

## Zanim zaczniesz

Zanim rozpoczniesz to ćwiczenie, upewnij się, że masz ukończone [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz inne zasoby używane w tym ćwiczeniu.

## Krok 1: Praca z zasobami danych

W tym kroku użyjesz kodu w notatniku, aby popracować z tabelarycznymi i plikowymi zasobami danych.

1. W [Azure Machine Learning studio](https://ml.azure.com) otwórz stronę **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że Twoja instancja obliczeniowa jest uruchomiona. Jeśli nie, uruchom ją.
2. Gdy instancja obliczeniowa jest uruchomiona, kliknij link **JupyterLab** przy tej instancji, aby otworzyć JupyterLab w nowej karcie przeglądarki.
3. W przeglądarce plików JupyterLab otwórz folder **MachineLearningCourse/materialy/azure-ml/pl**, a w nim notatnik **04B - Working with Datasets.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a potem czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

> **Uwaga**: Jeśli zamierzasz przejść od razu do [kolejnego ćwiczenia](Lab05A.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.
