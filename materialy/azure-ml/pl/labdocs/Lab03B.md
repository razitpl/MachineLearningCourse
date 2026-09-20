# Lab 3B: Trenowanie i rejestrowanie modeli

W uczeniu maszynowym chodzi ostatecznie o wytrenowanie modelu, który będzie dostarczał aplikacjom predykcji. Czas więc zobaczyć, jak uruchamiać skrypty trenujące jako zadania (jobs) Azure Machine Learning i jak zarejestrować powstały w ten sposób model.

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz pozostałe zasoby używane tutaj.

## Krok 1: Używanie Azure Machine Learning SDK do trenowania i rejestrowania modeli

W tym kroku uruchomisz z poziomu notatnika skrypty trenujące jako zadania (jobs) Azure Machine Learning. Wywołanie `mlflow.sklearn.autolog()` samo zaloguje parametry, metryki i wytrenowany model, a na koniec zarejestrujesz ten model pod nazwą `diabetes_model`.

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego; a na karcie **Compute Instances** upewnij się, że Twoja instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.
2. Gdy instancja obliczeniowa jest uruchomiona, kliknij link **JupyterLab**, aby otworzyć go w nowej karcie przeglądarki.
3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md) (`~/cloudfiles/code/Users/<twoja-nazwa-uzytkownika>/MachineLearningCourse/materialy/azure-ml/pl`), a następnie otwórz notatnik **03B - Training Models.ipynb**. Upewnij się, że używa on kernela **Python 3.10 - SDK v2**, a następnie przeczytaj notatki w notatniku, uruchamiając kolejno każdą komórkę kodu.

> **Uwaga**: Jeśli zamierzasz przejść od razu do [kolejnego ćwiczenia](Lab04A.md), pozostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i **zatrzymać** (Stop) instancję obliczeniową, aby uniknąć niepotrzebnych kosztów.
