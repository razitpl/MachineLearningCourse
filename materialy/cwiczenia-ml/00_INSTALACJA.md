# 00 - Przygotowanie środowiska pracy

Zanim zaczniesz ćwiczenia, potrzebujesz działającego środowiska Pythona. Ten dokument przeprowadzi Cię przez instalację **na własnym komputerze**.

> **Nie masz ochoty nic instalować?** Przeskocz do sekcji [Praca bez instalacji](#praca-bez-instalacji-w-chmurze) na końcu. Ćwiczenia działają w przeglądarce, bez dotykania własnego systemu.

## Co właściwie instalujemy i po co

| Element | Do czego służy |
|---|---|
| **Python** | język, w którym piszemy - sam w sobie nie umie uczenia maszynowego |
| **pandas** | wczytywanie i obróbka danych tabelarycznych (ramki danych, ang. *dataframes*) |
| **NumPy** | obliczenia na tablicach liczb; fundament, na którym stoi reszta |
| **scikit-learn** | właściwe uczenie maszynowe: modele, metryki, podział danych |
| **matplotlib** | wykresy |
| **JupyterLab** | środowisko, w którym otwierasz i uruchamiasz notatniki `.ipynb` |
| **joblib** | zapisywanie wytrenowanego modelu do pliku (ćwiczenie 10) |
| **pytest** | testy jednostkowe (ćwiczenie 11) |

Nie instalujemy niczego więcej - żadnego TensorFlow ani PyTorcha. Cały kurs opiera się na scikit-learn, który jest lekki i działa na każdym sprzęcie, także bez karty graficznej.

---

## Krok 1: Zainstaluj Pythona

Potrzebujesz **Pythona 3.10 lub nowszego**.

### Sprawdź, czy już go masz

Otwórz terminal (Windows: **PowerShell**; macOS/Linux: **Terminal**) i wpisz:

```bash
python --version
```

Jeśli zobaczysz `Python 3.10.x` lub nowszy - masz gotowe, przejdź do kroku 2.

> Na macOS i Linuksie może być potrzebne `python3 --version`. Jeśli tak, w dalszych poleceniach też używaj `python3` zamiast `python`.

### Jeśli Pythona nie ma

Masz dwie drogi. **Wybierz jedną.**

#### Droga A: Miniconda (polecana, jeśli zaczynasz)

Miniconda instaluje Pythona wraz z własnym menedżerem środowisk. Jest odporniejsza na typowe problemy z bibliotekami obliczeniowymi na Windowsie.

1. Pobierz instalator ze strony [docs.conda.io/projects/miniconda](https://docs.conda.io/projects/miniconda/en/latest/).
2. Zainstaluj, zostawiając ustawienia domyślne.
3. Uruchom **Anaconda Prompt** (Windows) albo zwykły terminal (macOS/Linux).

#### Droga B: Python z python.org

1. Pobierz instalator ze strony [python.org/downloads](https://www.python.org/downloads/).
2. **Windows - to jest ważne**: na pierwszym ekranie instalatora zaznacz **„Add Python to PATH"**. Bez tego terminal nie znajdzie Pythona i dostaniesz `'python' is not recognized`.
3. Dokończ instalację.

> **Której wersji Pythona użyć**: nie zawsze najnowszej. Biblioteki obliczeniowe potrzebują czasem kilku tygodni, żeby wypuścić paczki dla świeżo wydanej wersji Pythona. Jeśli przy instalacji pakietów zobaczysz błędy o kompilacji, zainstaluj Pythona o jedno wydanie starszego.

---

## Krok 2: Pobierz materiały

Notatniki, dane i lista pakietów leżą w publicznym repozytorium
[razitpl/MachineLearningCourse](https://github.com/razitpl/MachineLearningCourse).
Wybierz jedną z dwóch dróg.

### Droga A: git clone (polecana)

Wymaga zainstalowanego [Gita](https://git-scm.com/downloads), ale pozwala potem pobierać
poprawki jednym poleceniem `git pull` - a materiały bywają poprawiane w trakcie kursu.

```bash
git clone https://github.com/razitpl/MachineLearningCourse.git
```

### Droga B: archiwum ZIP

Bez Gita, za to bez aktualizacji - przy każdej poprawce trzeba pobrać całość od nowa.

1. Otwórz [repozytorium](https://github.com/razitpl/MachineLearningCourse) w przeglądarce.
2. Kliknij zielony przycisk **Code**, a potem **Download ZIP**.
3. Rozpakuj archiwum w dowolnym miejscu.

### Co dostajesz

Ćwiczenia z uczenia maszynowego są w podkatalogu `materialy/cwiczenia-ml`:

| Plik | Zawartość |
|---|---|
| `01_pierwszy_model.ipynb` … `11_jakosc_kodu_ml.ipynb` | notatniki z ćwiczeniami |
| `00_sprawdz_srodowisko.ipynb` | test poprawności instalacji |
| `dane/` | pliki CSV używane w ćwiczeniach |
| `requirements.txt` | lista pakietów do zainstalowania w kroku 4 |
| `00_INSTALACJA.md` | ten dokument |

Obok, w `materialy/azure-ml`, leżą materiały do kursu Azure Machine Learning - jeśli
realizujesz tylko część o uczeniu maszynowym, możesz je zignorować.

---

## Krok 3: Utwórz środowisko wirtualne

**Środowisko wirtualne** (ang. *virtual environment*) to odizolowany katalog z własnym kompletem pakietów.

Po co? Bo różne projekty potrzebują różnych wersji tych samych bibliotek. Bez izolacji instalacja czegoś na potrzeby jednego projektu potrafi zepsuć inny - klasyczny problem znany jako „piekło zależności". Środowisko wirtualne sprawia, że ten kurs ma swoją własną piaskownicę, a reszta systemu pozostaje nietknięta.

Przejdź do katalogu z ćwiczeniami:

```bash
cd sciezka/do/MachineLearningCourse/materialy/cwiczenia-ml
```

### Wariant conda (jeśli wybrałeś Minicondę)

```bash
conda create -n ml-kurs python=3.11
conda activate ml-kurs
```

### Wariant venv (jeśli wybrałeś Pythona z python.org)

```bash
python -m venv .venv
```

Aktywacja zależy od systemu:

```bash
# Windows (PowerShell)
.venv\Scripts\Activate.ps1

# Windows (cmd)
.venv\Scripts\activate.bat

# macOS / Linux
source .venv/bin/activate
```

Po aktywacji na początku wiersza polecenia zobaczysz nazwę środowiska, np. `(.venv)` albo `(ml-kurs)`. **To jest sygnał, że jesteś we właściwym miejscu** - bez tego pakiety zainstalują się globalnie.

> **Windows, błąd o zasadach wykonywania skryptów**: jeśli przy aktywacji PowerShell odmówi z komunikatem o `ExecutionPolicy`, uruchom raz:
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
> ```
> i spróbuj ponownie.

---

## Krok 4: Zainstaluj pakiety

Przy **aktywnym** środowisku:

```bash
pip install -r requirements.txt
```

Instalacja potrwa od kilkudziesięciu sekund do kilku minut - pobierane są paczki o łącznej wielkości kilkuset megabajtów.

---

## Krok 5: Uruchom JupyterLab

```bash
jupyter lab
```

W przeglądarce otworzy się JupyterLab. Po lewej stronie zobaczysz pliki - otwórz **`00_sprawdz_srodowisko.ipynb`** i uruchom wszystkie komórki (**Run → Run All Cells**).

Jeśli notatnik wypisze, że wszystko działa - jesteś gotowy do ćwiczenia 01.

> **Zamykanie**: JupyterLab działa w terminalu, w którym go uruchomiłeś. Żeby go zatrzymać, wróć do terminala i naciśnij `Ctrl+C`. Samo zamknięcie karty przeglądarki **nie** zatrzymuje serwera.

---

## Alternatywa: Visual Studio Code

Jeśli wolisz pracować w edytorze zamiast w przeglądarce:

1. Zainstaluj [Visual Studio Code](https://code.visualstudio.com/).
2. Doinstaluj rozszerzenia **Python** oraz **Jupyter** (obydwa od Microsoftu).
3. Otwórz katalog `cwiczenia-ml`, otwórz dowolny plik `.ipynb`.
4. W prawym górnym rogu kliknij **Select Kernel** i wskaż interpreter ze swojego środowiska (`.venv` albo `ml-kurs`).

Notatniki działają tak samo; zmienia się tylko interfejs.

---

## Typowe problemy

| Objaw | Przyczyna i rozwiązanie |
|---|---|
| `'python' is not recognized` (Windows) | Python nie trafił do PATH. Zainstaluj ponownie, zaznaczając **Add Python to PATH**, albo używaj `py` zamiast `python`. |
| `ModuleNotFoundError: No module named 'sklearn'` | Pakiety zainstalowane poza środowiskiem albo środowisko nieaktywne. Sprawdź, czy w wierszu polecenia widnieje `(.venv)`, i powtórz `pip install -r requirements.txt`. |
| JupyterLab nie widzi środowiska | Uruchamiaj `jupyter lab` **z aktywnego** środowiska. Jeśli to nie pomoże: `python -m ipykernel install --user --name ml-kurs`. |
| `FileNotFoundError: dane/diabetes.csv` | Notatnik uruchomiony z innego katalogu. JupyterLab musi być uruchomiony z katalogu `cwiczenia-ml`. Sprawdź w notatniku: `import os; print(os.getcwd())`. |
| Wykresy się nie pokazują | W notatniku są widoczne domyślnie. Jeśli nie - upewnij się, że komórka kończy się `plt.show()`. |
| Instalacja sypie błędami kompilacji | Zbyt świeża wersja Pythona. Zainstaluj wydanie o jedno starsze (np. 3.11 zamiast najnowszego). |

---

## Praca bez instalacji (w chmurze)

Jeśli nie chcesz lub nie możesz niczego instalować, masz dwie możliwości - w obu środowisko jest gotowe:

**Instancja obliczeniowa Azure Machine Learning** (jeśli realizujesz też kurs Azure w tym repozytorium): wszystkie pakiety są już zainstalowane. Uruchom instancję, otwórz JupyterLab i sklonuj repozytorium.

**GitHub Codespaces**: na stronie repozytorium wybierz **Code → Codespaces → Create codespace**. Dostajesz Visual Studio Code w przeglądarce. Po uruchomieniu wykonaj w terminalu:

```bash
pip install -r materialy/cwiczenia-ml/requirements.txt
```

---

## Sprawdź się przed zajęciami

Zanim przyjdziesz na pierwsze ćwiczenia, upewnij się, że:

- [ ] `python --version` zwraca 3.10 lub nowszy,
- [ ] środowisko wirtualne aktywuje się bez błędu,
- [ ] `pip install -r requirements.txt` przeszło do końca,
- [ ] `jupyter lab` otwiera się w przeglądarce,
- [ ] notatnik `00_sprawdz_srodowisko.ipynb` przechodzi wszystkie sprawdzenia.

Jeśli któryś punkt nie działa - zgłoś to **przed** zajęciami, a nie w ich trakcie. Rozwiązywanie problemów instalacyjnych zabiera czas, którego nie będzie na zajęciach.
