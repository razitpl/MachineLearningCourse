# Lab 5B: Środowiska obliczeniowe (gdzie wykonuje się kod)

Eksperymenty możesz uruchamiać na lokalnych zasobach obliczeniowych, ale gdy potrzebujesz większej skali, zwykle lepiej sięgnąć po zasoby obliczeniowe w chmurze.

## Zanim zaczniesz

Zanim zaczniesz to ćwiczenie, upewnij się, że masz ukończone [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz inne zasoby wykorzystywane w tym ćwiczeniu.

## Krok 1: Praca ze środowiskami obliczeniowymi

W tym kroku uruchomisz zadanie trenujące (training job) w środowisku obliczeniowym w chmurze, korzystając z Azure Machine Learning SDK v2.

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że Twoja instancja obliczeniowa jest uruchomiona. Jeśli nie, uruchom ją.
2. Gdy instancja obliczeniowa jest uruchomiona, kliknij link **JupyterLab**, aby otworzyć interfejs JupyterLab w nowej karcie przeglądarki.
3. W JupyterLab otwórz folder **MachineLearningCourse/materialy/azure-ml/pl** (repozytorium sklonowane w [Lab 1B](Lab01B.md) - jeśli go jeszcze nie ma, sklonuj je teraz), a w nim notatnik **05B - Working with Compute Targets.ipynb**. Upewnij się, że notatnik korzysta z kernela **Python 3.10 - SDK v2**, a potem czytaj komentarze, uruchamiając kolejno każdą komórkę kodu.

> **Uwaga**: Jeśli zamierzasz przejść od razu do [kolejnego ćwiczenia](Lab06A.md), pozostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.
