# Lab 7A: Tworzenie usługi wnioskowania w czasie rzeczywistym

## Po co jest to ćwiczenie?

Do tej pory model kończył swoje życie w obszarze roboczym: trenowanie, zapis, rejestracja. To dopiero połowa drogi. Model, do którego nie da się wysłać nowych danych i dostać odpowiedzi, nie przynosi nikomu pożytku - wiedza siedzi w pliku, do którego nikt nie ma dostępu.

To ćwiczenie zamyka tę lukę. Zarejestrowany model udostępnisz jako **punkt końcowy (endpoint)** czasu rzeczywistego, czyli adres HTTP, pod który dowolna aplikacja wysyła dane pacjenta i natychmiast dostaje przewidywanie. Taka usługa działa w trybie „pytanie - odpowiedź w ułamku sekundy" i sprawdza się wszędzie tam, gdzie ktoś czeka na wynik: w formularzu w gabinecie lekarskim, w aplikacji mobilnej, w systemie rejestracji pacjentów.

To moment, w którym uczenie maszynowe przestaje być eksperymentem, a staje się usługą. Reszta kursu dotyczy już tego, jak taką usługę obserwować i rozumieć.

> **Dwa sposoby wnioskowania**: *wnioskowanie (inferencing)* to po prostu uzyskiwanie przewidywań z gotowego modelu. Robi się je na dwa sposoby - w czasie rzeczywistym (pojedyncze zapytania, odpowiedź od razu) albo wsadowo (duża paczka danych przetwarzana naraz). To ćwiczenie dotyczy pierwszego sposobu, [Lab 7B](Lab07B.md) - drugiego.

## Czego się nauczysz

1. Czym jest punkt końcowy czasu rzeczywistego (*managed online endpoint*) i czym różni się od zwykłego zadania.
2. Jak napisać **skrypt scoringowy** - kod, który ładuje model i zamienia przychodzące dane na przewidywania.
3. Jak zdefiniować środowisko kontenera, w którym ten skrypt się wykonuje.
4. Jak wdrożyć model, skierować na wdrożenie ruch i sprawdzić, czy wdrożenie się powiodło.
5. Jak wywołać gotowy endpoint - z poziomu SDK oraz zwykłym zapytaniem HTTP, tak jak zrobiłaby to aplikacja kliencka.
6. Jak usunąć endpoint, gdy przestaje być potrzebny, żeby nie generował kosztów.

## Zanim zaczniesz

Upewnij się, że masz ukończone ćwiczenia [Lab 1A](Lab01A.md) i [Lab 1B](Lab01B.md) - powstaje w nich obszar roboczy Azure Machine Learning oraz pozostałe zasoby używane w tym ćwiczeniu.

## Krok 1: Wdrożenie modelu jako punktu końcowego czasu rzeczywistego

1. W [Azure Machine Learning studio](https://ml.azure.com) przejdź do strony **Compute** swojego obszaru roboczego, a na karcie **Compute instances** upewnij się, że instancja obliczeniowa (`mymachine`) jest uruchomiona. Jeśli nie, uruchom ją.

   > **Dlaczego to robimy**: instancja obliczeniowa jest maszyną, na której działa notatnik i z której zlecasz wdrożenie. Sam endpoint powstanie na osobnych, zarządzanych przez Azure maszynach - instancja obliczeniowa tylko wydaje polecenia i nie jest potrzebna, żeby gotowa usługa działała.

2. Gdy instancja obliczeniowa jest uruchomiona, kliknij przy niej link **JupyterLab**, aby otworzyć JupyterLab w nowej karcie przeglądarki.

3. W przeglądarce plików JupyterLab otwórz folder `MachineLearningCourse/materialy/azure-ml/pl` sklonowany w [Lab 1B](Lab01B.md), a w nim notatnik **07A - Creating a Real-time Inferencing Service.ipynb**. Upewnij się, że korzysta on z kernela **Python 3.10 - SDK v2**, a następnie czytaj komentarze w notatniku, uruchamiając kolejno każdą komórkę kodu.

   > **Dlaczego kernel jest ważny**: kernel decyduje, które środowisko Pythona wykonuje kod. Tylko kernel z SDK v2 ma zainstalowany pakiet `azure-ai-ml` - w innym pierwszy import zakończy się błędem `ModuleNotFoundError`.

## Krok 2: Na co zwrócić uwagę w notatniku

Notatnik prowadzi przez pełną ścieżkę wdrożenia. Warto wiedzieć, po co jest każdy jej etap.

### Trenowanie i rejestracja modelu na początku

Pierwsza część notatnika trenuje drzewo decyzyjne i rejestruje je jako `diabetes_model`. **Ten fragment wykonujesz zawsze**, niezależnie od tego, które wcześniejsze ćwiczenia masz za sobą.

> **Dlaczego to robimy**: skrypt scoringowy z tego ćwiczenia wczytuje model funkcją `joblib.load` z pliku `diabetes_model.pkl`, czyli oczekuje zwykłego modelu scikit-learn zarejestrowanego jako zasób typu `CUSTOM_MODEL`. Wcześniejsze ćwiczenia (3B, 6A) rejestrują `diabetes_model` w formacie MLflow, który ma inną strukturę katalogów. Uruchomienie tej komórki gwarantuje, że w obszarze roboczym jest wersja modelu w formacie, którego skrypt się spodziewa.

### Skrypt scoringowy

`score_diabetes.py` to **skrypt scoringowy** (ang. *scoring script*, nazywany też *entry script*) - kod, który uruchamia się wewnątrz kontenera endpointu. Ma dokładnie dwie funkcje:

| Funkcja | Kiedy się wykonuje | Co robi |
|---|---|---|
| `init()` | raz, przy starcie wdrożenia | wczytuje model do pamięci ze ścieżki ze zmiennej `AZUREML_MODEL_DIR` |
| `run(raw_data)` | przy każdym zapytaniu | zamienia JSON na tablicę, wywołuje `model.predict` i zwraca wynik |

> **Dlaczego akurat taki podział**: wczytanie modelu z dysku trwa. Gdyby działo się przy każdym zapytaniu, usługa byłaby wolna. `init()` płaci ten koszt raz, a `run()` korzysta z modelu trzymanego w pamięci - dlatego odpowiedź przychodzi w ułamku sekundy.

> **Dlaczego skrypt zwraca nazwy klas, a nie liczby**: model zwraca 0 albo 1. Aplikacja kliencka nie ma obowiązku wiedzieć, co te liczby znaczą, więc skrypt tłumaczy je na `not-diabetic` i `diabetic`. Zamiana surowego wyniku modelu na coś zrozumiałego dla odbiorcy to typowe zadanie skryptu scoringowego.

### Plik środowiska

Notatnik tworzy plik conda `diabetes_env.yml` z listą pakietów (`scikit-learn`, `numpy`, `azureml-inference-server-http`).

> **Dlaczego to robimy**: endpoint działa w kontenerze, który powstaje od zera - nie ma w nim nic poza tym, co wymienisz. Bez `scikit-learn` model się nie wczyta, bez `azureml-inference-server-http` nie zadziała serwer przyjmujący zapytania HTTP. Ten plik jest zarazem zapisem tego, na czym usługa działa, więc za pół roku da się ją odtworzyć w identycznej postaci.

### Samo wdrożenie

Notatnik tworzy endpoint `diabetes-endpoint` i w nim jedno wdrożenie o nazwie `blue`, a potem kieruje na nie 100% ruchu.

> **Dlaczego endpoint i wdrożenie to dwie różne rzeczy**: endpoint jest stabilnym adresem, który znają aplikacje klienckie. **Wdrożenie** to konkretna wersja modelu, kodu i środowiska ukryta za tym adresem. Rozdzielenie ich pozwala wystawić obok wdrożenia `blue` drugie, z nowszym modelem, przekierować na nie najpierw 10% ruchu, sprawdzić wyniki i dopiero potem przełączyć całość - wszystko bez zmiany adresu po stronie klienta. Stąd nazwa `blue`: to konwencja wdrożeń typu „blue-green".

> **Uwaga o czasie**: wdrożenie trwa kilkanaście minut, bo Azure musi zbudować obraz kontenera ze środowiska i uruchomić na nim maszynę. Gdy się powiedzie, stan wdrożenia (`provisioning_state`) ma wartość `Succeeded`. Jeśli nie - kolejna komórka pobiera logi kontenera; to pierwsze miejsce, w którym szuka się przyczyny błędu.

### Wywołanie endpointu

Notatnik korzysta z endpointu dwoma drogami: przez metodę `invoke` z SDK oraz przez zwykłe zapytanie `POST` biblioteką `requests`.

> **Dlaczego pokazujemy obie**: pierwsza jest wygodna do testów z poziomu Pythona. Druga pokazuje, jak wygląda to naprawdę w produkcji - aplikacja biznesowa nie ma zainstalowanego SDK Azure ML, wysyła po prostu JSON pod adres endpointu. Ponieważ endpoint powstał z `auth_mode="key"`, zapytanie musi zawierać nagłówek **Authorization** z kluczem; bez niego Azure odrzuci je jako nieuwierzytelnione.

### Sprzątanie

Na końcu notatnika jest zakomentowana komórka usuwająca endpoint.

> **Dlaczego to ważne**: endpoint czasu rzeczywistego utrzymuje uruchomioną maszynę, żeby móc odpowiedzieć w każdej chwili - i nalicza koszt przez cały ten czas, nawet gdy nie przychodzi ani jedno zapytanie. To zasadnicza różnica wobec zadań i punktów końcowych wsadowych, które płacą tylko za czas obliczeń. Jeśli endpoint nie jest już potrzebny, usuń go.

Możesz też obejrzeć efekt w interfejsie: w Azure Machine Learning studio otwórz stronę **Endpoints**, gdzie widać wdrożone punkty końcowe obszaru roboczego.

> **Uwaga**: Jeśli zamierzasz od razu przejść do [kolejnego ćwiczenia](Lab07B.md), zostaw instancję obliczeniową uruchomioną. Jeśli robisz przerwę, warto zamknąć wszystkie karty JupyterLab i zatrzymać instancję obliczeniową przyciskiem **Stop**, aby uniknąć niepotrzebnych kosztów.

## Co dalej

W [Lab 7B](Lab07B.md) rozwiążesz ten sam problem - udostępnienie modelu - ale w drugim wariancie: zamiast odpowiadać na pojedyncze zapytania, model przetworzy naraz setki plików z danymi pacjentów. Zobaczysz przy tym, że skrypt scoringowy i wdrożenie wyglądają podobnie, a różni je przede wszystkim to, skąd biorą się dane i dokąd trafiają wyniki.
