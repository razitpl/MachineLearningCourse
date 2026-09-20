# Lab 4A: Praca z magazynami danych (Datastores)

Chociaż analitycy danych często pracują z danymi na lokalnym systemie plików, w środowisku korporacyjnym efektywniejsze bywa przechowywanie danych w centralnej lokalizacji, do której jednocześnie ma dostęp wiele osób. W tym ćwiczeniu zapiszesz dane w chmurze i użyjesz *magazynu danych (datastore)* Azure Machine Learning, aby uzyskać do nich dostęp.

## Zanim zaczniesz

Zanim rozpoczniesz to ćwiczenie, upewnij się, że masz ukończone [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz inne zasoby używane w tym ćwiczeniu.

## Krok 1: Utwórz kontener usługi Azure Storage
W Azure Machine Learning możesz korzystać z wielu rodzajów źródeł danych platformy Azure. W tym ćwiczeniu utworzysz konto usługi Azure Storage zawierające kontener blob.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i otwórz grupę zasobów zawierającą Twój obszar roboczy Azure Machine Learning. Zwróć uwagę, że zawiera ona już konto usługi Azure Storage utworzone razem z obszarem roboczym.

    >**Uwaga**: Konto magazynu utworzone wraz z obszarem roboczym jest używane przez usługę do przechowywania danych konfiguracyjnych, notatników, zarejestrowanych modeli itd. Możesz go również wykorzystać do przechowywania danych na potrzeby eksperymentów i trenowania modeli, ale w wielu przypadkach wygodniej jest zarządzać tymi danymi osobno.

2. Dodaj nowe **konto magazynu (Storage account)** do grupy zasobów, z następującymi ustawieniami:

    - **Storage account name**: unikalna nazwa.
    - **Location**: ta sama lokalizacja co Twój obszar roboczy
    - **Performance**: Standard
    - **Account kind**: StorageV2 (general purpose v2)
    - **Access tier (default)**: Hot
    - Użyj domyślnych ustawień sieciowych

3. Poczekaj, aż konto magazynu zostanie utworzone, a następnie przejdź do tego zasobu w portalu.
4. Otwórz stronę **Containers** dla konta magazynu i dodaj kontener z następującymi ustawieniami:

    - **Name**: aml-data
    - **Public access level**: Private (no anonymous access)

5. Po dodaniu kontenera przejdź do strony **Access Keys** swojego konta magazynu i skopiuj **key1** do schowka - będzie potrzebny w kolejnym kroku.

## Krok 2: Zarejestruj magazyn danych Azure Machine Learning

Kontener magazynu jest już gotowy, więc można go zarejestrować jako magazyn danych (datastore) w obszarze roboczym Azure Machine Learning.

1. W [Azure Machine Learning studio](https://ml.azure.com) otwórz stronę **Datastores** swojego obszaru roboczego. Wyświetlona zostanie lista predefiniowanych magazynów danych.
2. Utwórz nowy magazyn danych z następującymi ustawieniami:
    - **Datastore name**: aml_data
    - **Datastore type**: Azure Blob Storage
    - **Account selection method**: From Azure subscription
    - **Subscription ID**: *Twoja subskrypcja Azure*
    - **Storage account**: *konto magazynu utworzone w poprzednim kroku*
    - **Blob container**: aml-data
    - **Authentication type**: Account key
    - **Account key**: *wklej klucz skopiowany w poprzednim kroku*
3. Po dodaniu magazynu danych sprawdź, czy znajduje się on na liście na stronie **Datastores**.

## Krok 3: Użyj Azure Machine Learning SDK v2, aby uzyskać dostęp do magazynu danych

W tym kroku użyjesz Azure Machine Learning SDK v2, aby zarejestrować zasób danych z plików lokalnych i wykorzystać go jako wejście (input) zadania trenującego (job).

1. W [Azure Machine Learning studio](https://ml.azure.com) otwórz stronę **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że Twoja instancja obliczeniowa jest uruchomiona. Jeśli nie, uruchom ją.
2. Gdy instancja obliczeniowa jest uruchomiona, kliknij link **JupyterLab** przy tej instancji, aby otworzyć JupyterLab w nowej karcie przeglądarki.
3. W przeglądarce plików JupyterLab otwórz folder **MachineLearningCourse/materialy/azure-ml/pl** (repozytorium sklonowane w [Lab 1B](Lab01B.md) - jeśli go jeszcze nie ma, sklonuj je teraz), a następnie otwórz notatnik **04A - Working with Datastores.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a potem czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

> **Uwaga**: Jeśli zamierzasz przejść od razu do [kolejnego ćwiczenia](Lab04B.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.
