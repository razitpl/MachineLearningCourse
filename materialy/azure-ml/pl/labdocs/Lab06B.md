# Lab 6B: Publikowanie potoku

Gotowy potok możesz wdrożyć za punktem końcowym wsadowym (batch endpoint), który służy do jego uruchamiania - na żądanie albo według harmonogramu.

## Zanim zaczniesz

Zanim rozpoczniesz to ćwiczenie, upewnij się, że masz ukończone [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz inne zasoby używane w tym ćwiczeniu - a także [Lab 6A](Lab06A.md), w którym powstaje potok wdrażany w tym ćwiczeniu.

## Krok 1: Wdrożenie potoku jako punktu końcowego wsadowego

W tym kroku wdrożysz za punktem końcowym wsadowym potok utworzony w poprzednim ćwiczeniu.

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że Twoja instancja obliczeniowa jest uruchomiona. Jeśli nie, uruchom ją.
2. Gdy instancja obliczeniowa jest uruchomiona, kliknij link **JupyterLab** przy tej instancji, aby otworzyć JupyterLab w nowej karcie przeglądarki.
3. W przeglądarce plików JupyterLab otwórz folder **MachineLearningCourse/materialy/azure-ml/pl** (repozytorium sklonowane w [Lab 1B](Lab01B.md)), a w nim notatnik **06B - Publishing a Pipeline.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a potem czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

> **Uwaga**: Jeśli zamierzasz od razu przejść do [kolejnego ćwiczenia](Lab07A.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.
