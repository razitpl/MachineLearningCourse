# Lab 1B: Praca z narzędziami Azure Machine Learning

W tym ćwiczeniu poznasz narzędzia, którymi pracuje się z obszarem roboczym Azure Machine Learning.

Będziesz korzystać z Azure Machine Learning studio, JupyterLab na instancji obliczeniowej, GitHub oraz Azure Machine Learning SDK v2 dla Pythona.

## Zanim zaczniesz

Potrzebujesz obszaru roboczego Azure Machine Learning utworzonego zgodnie z instrukcjami z [poprzedniego ćwiczenia](Lab01A.md).

Potrzebujesz również:

- Subskrypcji Azure z uprawnieniami dostępu do obszaru roboczego Azure Machine Learning.
- Uruchomionej instancji obliczeniowej Azure Machine Learning.
- Dostępu do repozytorium kursu.

## Krok 1: Użyj Azure ML SDK v2 w instancji obliczeniowej

Wieloma zasobami Azure Machine Learning można zarządzać w interfejsie studio. Azure Machine Learning SDK v2 pozwala jednak zrobić to samo z poziomu kodu Pythona i zautomatyzować powtarzalne czynności.

1. Otwórz [Azure Machine Learning studio](https://ml.azure.com).

2. Wybierz swój obszar roboczy Azure Machine Learning.

3. W menu po lewej stronie wybierz **Compute**.

4. Otwórz kartę **Compute instances**.

5. Uruchom instancję obliczeniową utworzoną w poprzednim ćwiczeniu, jeśli jeszcze nie działa.

6. Poczekaj, aż status instancji obliczeniowej zmieni się na **Running**.

7. Kliknij link **JupyterLab** przy tej instancji obliczeniowej.

8. W JupyterLab utwórz nowy notatnik.

9. W prawym górnym rogu notatnika wybierz aktualny kernel Azure Machine Learning SDK v2. Nazwa kernela może być podobna do:

    ```text
    Python 3.10 - SDK v2
    ```

10. Uruchom poniższą komórkę z kodem, aby sprawdzić, czy Azure ML SDK v2 jest dostępny:

    ```python
    import sys
    from azure.ai.ml import MLClient
    from azure.identity import DefaultAzureCredential

    print(f"Interpreter Pythona: {sys.executable}")
    print("Azure Machine Learning SDK v2 jest gotowy.")
    ```

11. Jeśli importy zakończą się błędem `ModuleNotFoundError`, otwórz nowy terminal w JupyterLab i zainstaluj wymagane pakiety:

    ```bash
    python -m pip install --upgrade azure-ai-ml azure-identity
    ```

12. Po zainstalowaniu pakietów zrestartuj kernel notatnika i uruchom ponownie komórkę weryfikującą.

> **Więcej informacji:** Aktualną dokumentację Azure Machine Learning SDK v2 znajdziesz w [dokumentacji Azure ML SDK dla Pythona](https://learn.microsoft.com/python/api/overview/azure/ai-ml-readme?view=azure-python).

## Krok 2: Sklonuj repozytorium kursu

Będziesz korzystać z notatników i plików pomocniczych z repozytorium kursu.

1. W JupyterLab wybierz **File** > **New** > **Terminal**.

2. Przejdź do swojego katalogu użytkownika Azure Machine Learning, zastępując `<your-user-name>` nazwą folderu widoczną w sekcji **Users** w przeglądarce plików JupyterLab (bierze się ona z Twojej tożsamości Azure AD, a nie z nazwy użytkownika w systemie Linux):

    ```bash
    cd ~/cloudfiles/code/Users/<your-user-name>
    ```

3. Sklonuj repozytorium kursu:

    ```bash
    git clone https://github.com/razitpl/MachineLearningCourse.git
    ```

4. Jeśli repozytorium zostało już wcześniej sklonowane, zamiast tego je zaktualizuj:

    ```bash
    cd ~/cloudfiles/code/Users/<your-user-name>/MachineLearningCourse/materialy/azure-ml/pl
    git pull
    ```

5. Zamknij kartę terminala.

6. W przeglądarce plików JupyterLab w razie potrzeby odśwież stronę.

7. Otwórz następujący folder:

    ```text
    MachineLearningCourse/materialy/azure-ml/pl
    ```

8. Znajdź i otwórz notatnik **01B - Intro to the Azure ML SDK.ipynb** do tego ćwiczenia.

9. Upewnij się, że notatnik korzysta z kernela **Python 3.10 - SDK v2** lub innego aktualnego kernela Azure ML SDK v2.

10. Uruchamiaj komórki notatnika pojedynczo i czytaj wyjaśnienia.

## Krok 3: Połącz się z obszarem roboczym Azure ML

Azure Machine Learning SDK v2 używa obiektu `MLClient` do łączenia się z obszarem roboczym Azure Machine Learning i zarządzania nim.

Możesz się połączyć, podając w kodzie identyfikator subskrypcji, nazwę grupy zasobów i nazwę obszaru roboczego. Możesz też pobrać z portalu Azure plik `config.json` i użyć go do utworzenia klienta.

### Pobierz konfigurację obszaru roboczego

1. Otwórz [portal Azure](https://portal.azure.com) w nowej karcie przeglądarki.

2. Znajdź i otwórz obszar roboczy Azure Machine Learning utworzony w poprzednim ćwiczeniu.

3. Na stronie **Overview** obszaru roboczego wybierz **Download config.json**.

4. Zapisz pobrany plik.

5. W JupyterLab wgraj plik `config.json` do folderu głównego repozytorium `MachineLearningCourse/materialy/azure-ml/pl`.

6. Zadbaj o to, żeby plik `config.json` nie trafił do publicznego repozytorium GitHub - zawiera dane Twojego obszaru roboczego.

7. Jeśli repozytorium nie zawiera jeszcze pliku `.gitignore`, utwórz go.

8. Dodaj do `.gitignore` następujący wpis:

    ```gitignore
    config.json
    ```

### Połącz się za pomocą Azure ML SDK v2

1. Otwórz notatnik korzystający z kernela **Python 3.10 - SDK v2**.

2. Uruchom następującą komórkę:

    ```python
    from azure.ai.ml import MLClient
    from azure.identity import DefaultAzureCredential

    credential = DefaultAzureCredential()

    ml_client = MLClient.from_config(
        credential=credential
    )

    print(f"Połączono z obszarem roboczym: {ml_client.workspace_name}")
    ```

3. Jeśli uwierzytelnianie się nie powiedzie, otwórz terminal JupyterLab i zaloguj się do Azure:

    ```bash
    az login
    ```

4. Dokończ logowanie w przeglądarce.

5. Wróć do notatnika i ponownie uruchom komórkę nawiązującą połączenie.

6. Gdy połączenie się powiedzie, sprawdź, czy wyświetlana jest poprawna nazwa obszaru roboczego Azure Machine Learning.

> **Uwaga:** `DefaultAzureCredential` może korzystać z wielu metod uwierzytelniania. Przy pracy interaktywnej najprostsze jest zwykle logowanie przez Azure CLI.

## Krok 4: Poznaj zasoby Azure ML za pomocą SDK v2

Po połączeniu się z obszarem roboczym możesz użyć `MLClient` do wyświetlania listy zasobów Azure Machine Learning i zarządzania nimi.

1. Uruchom poniższy kod, aby wyświetlić listę dostępnych zasobów obliczeniowych:

    ```python
    for compute in ml_client.compute.list():
        print(f"{compute.name}: {compute.type}")
    ```

2. Uruchom poniższy kod, aby wyświetlić listę zarejestrowanych zasobów danych:

    ```python
    for data_asset in ml_client.data.list():
        print(
            f"Nazwa: {data_asset.name}, "
            f"Wersja: {data_asset.version}, "
            f"Typ: {data_asset.type}"
        )
    ```

3. Uruchom poniższy kod, aby wyświetlić listę zarejestrowanych modeli:

    ```python
    for model in ml_client.models.list():
        print(
            f"Nazwa: {model.name}, "
            f"Wersja: {model.version}, "
            f"Typ: {model.type}"
        )
    ```

4. Uruchom poniższy kod, aby wyświetlić listę ostatnich zadań (jobs) Azure Machine Learning:

    ```python
    for job in ml_client.jobs.list():
        print(
            f"Nazwa: {job.name}, "
            f"Status: {job.status}, "
            f"Nazwa wyświetlana: {job.display_name}"
        )
    ```

5. Porównaj zasoby zwrócone przez SDK z zasobami wyświetlanymi w Azure Machine Learning studio.

> **Uwaga:** SDK v2 umożliwia programowe zarządzanie zasobami danych, środowiskami, zasobami obliczeniowymi, zadaniami, modelami i punktami końcowymi (endpoints). Skorzystasz z tych możliwości w kolejnych ćwiczeniach.

## Krok 5: Opcjonalna praca w GitHub Codespaces

Z repozytorium można też pracować poza Azure Machine Learning studio - w GitHub Codespaces.

GitHub Codespaces udostępnia środowisko programistyczne Visual Studio Code działające w przeglądarce.

1. Otwórz [repozytorium MachineLearningCourse/materialy/azure-ml/pl](https://github.com/razitpl/MachineLearningCourse).

2. Wybierz **Code**.

3. Wybierz kartę **Codespaces**.

4. Wybierz **Create codespace on main**.

5. Poczekaj na uruchomienie Codespace.

6. Otwórz zintegrowany terminal w Visual Studio Code.

7. Utwórz wirtualne środowisko Pythona:

    ```bash
    python -m venv .venv
    ```

8. Aktywuj wirtualne środowisko:

    ```bash
    source .venv/bin/activate
    ```

9. Zaktualizuj `pip` i zainstaluj Azure ML SDK v2:

    ```bash
    python -m pip install --upgrade pip
    python -m pip install azure-ai-ml azure-identity ipykernel
    ```

10. Zaloguj się do Azure:

    ```bash
    az login
    ```

11. W Visual Studio Code wybierz interpreter Pythona z wirtualnego środowiska `.venv`.

12. Otwórz notatnik ćwiczenia i w razie potrzeby wybierz kernel Pythona `.venv`.

> **Uwaga:** GitHub Codespaces jest opcjonalny. Wszystkie ćwiczenia da się wykonać w JupyterLab na instancji obliczeniowej Azure Machine Learning.

## Sprzątanie

Po zakończeniu pracy zatrzymaj instancję obliczeniową, jeśli nie planujesz jej używać w najbliższym czasie.

1. Wróć do [Azure Machine Learning studio](https://ml.azure.com).

2. Wybierz **Compute**.

3. Otwórz kartę **Compute instances**.

4. Wybierz swoją instancję obliczeniową.

5. Wybierz **Stop**.

> **Uwaga:** Zatrzymanie instancji obliczeniowej zapobiega dalszemu naliczaniu opłat za zasoby obliczeniowe. Możesz ją uruchomić ponownie, gdy wrócisz do kolejnych ćwiczeń.
