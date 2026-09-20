# Ćwiczenia z uczenia maszynowego

Zestaw praktycznych ćwiczeń do przedmiotu **Machine Learning**. Materiał jest niezależny od Azure Machine Learning - wszystkie notatniki uruchomisz w JupyterLab (na instancji obliczeniowej Azure ML, na własnym komputerze albo w GitHub Codespaces).

Nacisk położony jest na **samodzielne pisanie kodu**. Każdy notatnik ma tę samą budowę:

1. **Po co to ćwiczenie** - jaki problem rozwiązujemy i dlaczego ten temat jest ważny.
2. **Przykład prowadzony** - kod z wyjaśnieniem krok po kroku, do przeczytania i uruchomienia.
3. **Zadania** - miejsca oznaczone `# TWÓJ KOD TUTAJ`, które uzupełniasz samodzielnie. To jest główna część pracy.
4. **Pytania do przemyślenia** - bez kodu; sprawdzają, czy rozumiesz, *dlaczego* coś działa.
5. **Chcesz wiedzieć więcej** - linki do dokumentacji i dalszej lektury.

## Program ćwiczeń

| Nr | Temat | Czego się nauczysz |
|---|---|---|
| 00 | [Przygotowanie środowiska](00_INSTALACJA.md) + [sprawdzenie](00_sprawdz_srodowisko.ipynb) | Instalacja Pythona i pakietów na własnym komputerze, środowiska wirtualne, uruchomienie JupyterLab - **do zrobienia przed pierwszymi zajęciami** |
| 01 | [Twój pierwszy model](01_pierwszy_model.ipynb) | Cały cykl ML od danych do oceny modelu; po co dzielić dane; dlaczego zawsze zaczynamy od modelu odniesienia |
| 02 | [Poznaj swoje dane (EDA)](02_poznaj_swoje_dane.ipynb) | Statystyki opisowe, rozkłady, korelacje, wykrywanie wartości odstających i braków |
| 03 | [Przygotowanie danych](03_przygotowanie_danych.ipynb) | Braki danych, skalowanie, kodowanie zmiennych kategorycznych, `Pipeline` i `ColumnTransformer` |
| 04 | [Regresja](04_regresja.ipynb) | Regresja liniowa, regularyzacja (Ridge, Lasso), niedouczenie i przeuczenie |
| 05 | [Klasyfikacja i metryki](05_klasyfikacja_metryki.ipynb) | Macierz pomyłek, precyzja, czułość, F1, krzywa ROC, przesuwanie progu decyzyjnego |
| 06 | [Walidacja i dobór modelu](06_walidacja_dobor_modelu.ipynb) | Walidacja krzyżowa, `GridSearchCV`, krzywe uczenia, przeciek danych |
| 07 | [Drzewa i lasy](07_drzewa_i_lasy.ipynb) | Drzewa decyzyjne, lasy losowe, boosting, ważność cech |
| 08 | [Uczenie nienadzorowane](08_uczenie_nienadzorowane.ipynb) | Grupowanie (k-średnich), redukcja wymiarowości (PCA) |
| 09 | [Projekt końcowy](09_projekt_koncowy.ipynb) | Samodzielne przejście pełnej ścieżki na nowym zbiorze danych |
| 10 | [Od notatnika do skryptu](10_od_notatnika_do_skryptu.ipynb) | Przeniesienie modelu z notatnika do pliku `.py`, parametry z wiersza poleceń, zapis modelu na dysk, powtarzalność wyników |
| 11 | [Jakość kodu w ML](11_jakosc_kodu_ml.ipynb) | Wydzielenie funkcji, testy jednostkowe w `pytest`, sprawdzanie stylu kodu - czyli dlaczego „działa u mnie w notatniku" to za mało |

> **Dlaczego ćwiczenia 10 i 11 są na końcu, a nie na początku**: żeby zrozumieć, po co przenosić kod do skryptu i go testować, trzeba najpierw kilka razy poczuć ból pracy wyłącznie w notatniku - zgubione wyniki, komórki uruchomione w złej kolejności, model, którego nie da się odtworzyć. Ćwiczenia 01-09 ten ból wytwarzają, a 10-11 pokazują lekarstwo.

## Czego potrzebujesz

- Python 3.10 lub nowszy
- pakiety wymienione w [`requirements.txt`](requirements.txt)

**Pracujesz na własnym komputerze?** Zacznij od [`00_INSTALACJA.md`](00_INSTALACJA.md) - instrukcja krok po kroku, z rozwiązaniami typowych problemów. Potem uruchom [`00_sprawdz_srodowisko.ipynb`](00_sprawdz_srodowisko.ipynb), żeby potwierdzić, że wszystko działa.

**Pracujesz na instancji obliczeniowej Azure ML albo w GitHub Codespaces?** Pakiety są już zainstalowane (w Codespaces wykonaj `pip install -r materialy/cwiczenia-ml/requirements.txt`). Możesz od razu przejść do ćwiczenia 01.

W skrócie, dla niecierpliwych:

```bash
git clone https://github.com/razitpl/MachineLearningCourse.git
cd MachineLearningCourse/materialy/cwiczenia-ml
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\Activate.ps1
pip install -r requirements.txt
jupyter lab
```

## Terminologia

Materiał jest po polsku, ale **każdy ważny termin dostaje przy pierwszym użyciu angielski odpowiednik** w nawiasie:

> `X` to **cechy** (ang. *features*), `y` to **etykieta** (ang. *label*).

Powód jest praktyczny. Dokumentacja scikit-learn, komunikaty błędów, artykuły i ogłoszenia o pracę są po angielsku - znając wyłącznie polskie nazwy, nie znajdziesz niczego w wyszukiwarce. Odwrotnie też: znając wyłącznie angielskie, nie nadążysz za polskim wykładem ani podręcznikiem.

## Wykresy i wizualizacje

Wszystkie rysunki powstają w **matplotlibie** - i tylko w nim. Dotyczy to także wizualizacji drzew decyzyjnych, do których używamy `sklearn.tree.plot_tree`:

```python
from sklearn.tree import plot_tree
import matplotlib.pyplot as plt
```

Świadomie **nie korzystamy z Graphviza** (`export_graphviz`, `pydot`, `dtreeviz`). Wymagałby instalacji dodatkowego programu poza Pythonem, co na współdzielonej instancji obliczeniowej albo bez uprawnień administratora bywa niewykonalne. `plot_tree` daje czytelny rysunek bez żadnych dodatkowych zależności.

## Skąd biorą się dane

Podstawowy zbiór kursu to [`dane/diabetes.csv`](dane/) - 10 000 anonimowych kart pacjentów przebadanych pod kątem cukrzycy. Wracamy do niego niemal w każdym ćwiczeniu, i to jest celowe: **poznajesz jeden zbiór naprawdę dobrze**, zamiast za każdym razem tracić czas na zrozumienie nowych danych. Dzięki temu różnice między wynikami wynikają z metody, którą właśnie poznajesz, a nie z tego, że dane są inne.

Kolumny: osiem wyników badań (glukoza, ciśnienie, BMI, wiek i pozostałe) jako **cechy** (ang. *features*), kolumna `Diabetic` jako **etykieta** (ang. *label*) oraz `PatientID`, czyli numer pacjenta - który **nigdy nie jest cechą** (dlaczego, dowiesz się w ćwiczeniu 01).

Tam, gdzie temat tego wymaga, sięgamy dodatkowo po zbiory **wbudowane w scikit-learn** (np. w projekcie końcowym potrzebny jest zbiór, którego wcześniej nie widziałeś) albo po dane generowane syntetycznie, gdy chcemy mieć pełną kontrolę nad kształtem zależności.

Niczego nie pobieramy z internetu - wszystkie ćwiczenia działają bez dostępu do sieci.

> Ten sam plik `diabetes.csv` jest używany w kursie **Azure Machine Learning** prowadzonym w tym repozytorium. Jeśli realizujesz oba przedmioty, zobaczysz te same dane z dwóch stron: tutaj od strony metod uczenia maszynowego, tam od strony uruchamiania ich w chmurze.

## Jak pracować z tymi ćwiczeniami

- **Uruchamiaj komórki po kolei.** Późniejsze komórki korzystają ze zmiennych zdefiniowanych wcześniej.
- **Nie kopiuj rozwiązań bezmyślnie.** Zadania są celowo tak dobrane, żeby dało się je zrobić po przeczytaniu przykładu prowadzonego kilka komórek wyżej.
- **Eksperymentuj.** Zmień parametr, zepsuj coś celowo, zobacz co się stanie. Zepsuty model, który rozumiesz, uczy więcej niż działający, którego nie rozumiesz.
- **Czytaj komunikaty błędów do końca.** W uczeniu maszynowym większość błędów to niezgodność kształtów tablic albo typów danych - komunikat mówi dokładnie, co się nie zgadza.

## Uwaga o wynikach losowych

Wiele algorytmów ma element losowy (podział danych, inicjalizacja). Dlatego w kodzie pojawia się `random_state=42` - ustalenie ziarna losowości sprawia, że przy każdym uruchomieniu dostaniesz **te same wyniki**. To ważne, gdy porównujesz modele: inaczej nie wiedziałbyś, czy różnica bierze się ze zmiany modelu, czy z innego losowania.
