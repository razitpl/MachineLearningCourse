# Lab 5A: Środowiska uruchomieniowe (pakiety i kontenery)

Każdy kod Pythona działa w kontekście środowiska, które określa dostępne pakiety. Uruchamiając skrypt jako eksperyment w Azure Machine Learning, możesz sam zdecydować, w jakim środowisku ma się on wykonać.

## Zanim zaczniesz

Zanim zaczniesz to ćwiczenie, upewnij się, że masz ukończone [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz inne zasoby wykorzystywane w tym ćwiczeniu.

## Krok 1: Praca ze środowiskami

W tym kroku użyjesz kodu w notatniku, aby za pomocą Azure Machine Learning SDK v2 popracować ze środowiskami dla zadań (jobs) Azure Machine Learning.

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że Twoja instancja obliczeniowa jest uruchomiona. Jeśli nie, uruchom ją.
2. Gdy instancja obliczeniowa jest uruchomiona, kliknij link **JupyterLab**, aby otworzyć interfejs JupyterLab w nowej karcie przeglądarki.
3. W JupyterLab otwórz folder **MachineLearningCourse/materialy/azure-ml/pl** (repozytorium sklonowane w [Lab 1B](Lab01B.md) - jeśli go jeszcze nie ma, sklonuj je teraz), a w nim notatnik **05A - Working with Environments.ipynb**. Upewnij się, że notatnik korzysta z kernela **Python 3.10 - SDK v2**, a potem czytaj komentarze, uruchamiając kolejno każdą komórkę kodu.

> **Uwaga**: Jeśli zamierzasz przejść od razu do [kolejnego ćwiczenia](Lab05B.md), pozostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.
