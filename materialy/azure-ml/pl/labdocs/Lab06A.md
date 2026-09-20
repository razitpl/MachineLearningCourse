# Lab 6A: Tworzenie potoku (pipeline)

Za pomocą Azure Machine Learning SDK v2 wykonasz wszystkie czynności potrzebne do zbudowania i utrzymania rozwiązania uczenia maszynowego w Azure. Zamiast uruchamiać je pojedynczo, możesz połączyć je w *potok* (pipeline), który spina komponenty odpowiedzialne za przygotowanie danych, uruchomienie skryptów trenujących, rejestrację modelu i pozostałe etapy.

## Zanim zaczniesz

Zanim rozpoczniesz to ćwiczenie, upewnij się, że masz ukończone [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz inne zasoby używane w tym ćwiczeniu.

## Krok 1: Tworzenie potoku

W tym kroku utworzysz potok, który wytrenuje i zarejestruje model.

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że Twoja instancja obliczeniowa jest uruchomiona. Jeśli nie, uruchom ją.
2. Gdy instancja obliczeniowa jest uruchomiona, kliknij link **JupyterLab** przy tej instancji, aby otworzyć JupyterLab w nowej karcie przeglądarki.
3. W przeglądarce plików JupyterLab otwórz folder **MachineLearningCourse/materialy/azure-ml/pl** (repozytorium sklonowane w [Lab 1B](Lab01B.md)), a w nim notatnik **06A - Creating a Pipeline.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a potem czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

> **Uwaga**: Jeśli zamierzasz od razu przejść do [kolejnego ćwiczenia](Lab06B.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.
