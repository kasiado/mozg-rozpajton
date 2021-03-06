* funkcje  
* `matplotlib` - czyli tworzenie wykresów w pythonie
* pierwsze kroki w `mne` (wczytywanie, wyświetlanie)
* w ostatniej chwili - rysujemy sygnał eeg

## Jupyter notebook
Poznajemy środowisko Jupyter notebook.
* pokazówka
* korzystając z terminala idziemy tam gdzie chcemy rozpocząć pracę a następnie piszemy `jupyter notebook`
* wykonywanie komórek: `shift`+`enter` gdy chcemy przejść do kolejnej komórki, `ctrl`+`enter` gdy chcemy pozostać w bieżącej (jest jeszcze `alt`+`enter` - wykonuje bieżącą komórkę i tworzy nową poniżej).
* notebook możemy zapisać - jest to plik o rozszerzeniu `.ipynb`

## Piszemy funkcje

* zadanie główne (prawdopodobnie do domu): funkcja `czy_polak`
* zaczniemy jednak od banalnej funkcji `dodaj`
* schemat funkcji:
  ```python
  def nazwa_funkcji(argument):
      # coś robimy z argumentem
  ```
  
* funkcja `dodaj`:
  ```python
  def dodaj(a, b):
      return a + b
  ```
`return` to specjalna komenda - kończy funkcję i zwraca jakąś wartość. Na przykład:
```python
def głupia_funkcja():
    return 2 + 4
```
ta funkcja zawsze zwraca 6:
```python
głupia_funkcja()
6

głupia_funkcja() > 4
True
```

* w jakich sytuacjach funkcje są wygodne? Przypomnijcie sobie, co robiliśmy ostatnio:
```python
import numpy as np
from showit import image

# tworzymy macierz losowych wartości (10 wierszy na 10 kolumn)
A = np.random.rand(10, 10)

# chcemy wyświetlać wykresy nie w oddzielnym okienku, ale w konsoli/notebook'u:
# komenda poniżej działa tylko w jupyter notebook albo jupyter qtconsole
%matplotlib inline

# wyświetlamy tę macierz:
image(A)

# chcemy jednak w kolorze:
image(A, cmap='viridis')

# albo jak kotś woli:
image(A, cmap='magma')
```

Powiedzmy, że bardzo podoba nam się mapa kolorystyczna `viridis` i chcemy domyślnie z jej użyciem wyświetlać sobie macierze.
Piszemy więc funkcję!
```python
def img(matrix):
    image(matrix, cmap='viridis')
```
Teraz `img(A)` wyświetli nam macierz z mapą kolorystyczną `viridis`. W ten sposób oszczędzamy sobie pisania. O to właśnie chodzi w funkcjach - skracamy wielokrotnie wykonywane operacje (czasami nawet kilka-kilkanaście albo i kilkadziesiąt linijek kodu) do jednego wywołania funkcji.

* funkcja `czy_konczysie`, ma działać tak:
```python
czy_konczysie('misie konczysie', 'ysie')
# zwraca nam True
```
* poprawka do funkcji `czy_konczysie` - podajemy listę możliwych końcówek
* (ewentualnie) - zróbmy tak aby działało dla listy i dla tekstu - to trudniejsze `isinstance`
  
* jesteśmy wreszcie gotowi by napisać funkcję `czy_polak` a następnie zastosować ją do gąszczu autorów jednej z ostatnich prac na temat bożonu Higgsa aby wydobyć "naszych". Funkcja `czy_polak` ma sprawdzać czy końcówka nazwiska brzmi "polsko". Aby uprościć sobie zadanie stworzymy listę końcówek nazwisk wszystkich uczestników.

dodatkowe:
* Gdy już zrobimy to pętlą - robimy za pomocą comprehension
* następnie tworzymy zmienną `nazwiska` oraz korzystamy z funkcji `filter`


## Matplotlib
Matplotlib to główna biblioteka do plotowania w pythonie ("plotowanie" to wyświetlanie grafik takich jak wykresy liniowe itp.).
Konwencja importu jest taka:
```python
import matplotlib.pyplot as plt
```

podstawowe:
* proste plotowanie (`plt.plot`) - tworzymy wektor 25 wartości losowych i wyświetlamy
* korzystamy z x i y: tworzymy wektor 1000 równo rozmieszczonych wartości pomiędzy -10 i 10:
  ```python
  x = np.linspace(-10, 10, num=1000)
  ```
  
  tworzymy zmienną `y` - sinus z wartości `x`:
  ```python
  y = np.sin(x)
  ```
  wyświetlamy:
  ```python
  plt.plot(x, y)
  ```
* styl linii (`--r`), kolor(`color='g'` albo `color=[0.4, 0.9, 0.5]`), grubość (`lw=3`)
* bogata dokumentacja: zarówno `numpy` jak i `matplotlib`

Tego nie zrobiliśmy:
* dodawanie tytułu (`plt.title`), opisów osi (`plt.xlabel`, `plt.ylabel`)
* `plt.style.use`
* `plt.scatter`
* legenda i `label=`
* ewentualnie `plt.imshow`

  
## Pierwsze kroki w `mne`
Jesteśmy już gotowi aby postawić kilka pierwszych kroków w `mne` - pakiecie do analizy danych elektrofizjologicznych. Zrobimy na razie tylko kilka podstawowych operacji, a później wrócimy do dalszej nauki pythona. Robimy tak abyście nie musieli czekać do przedostatnich/ostatnich zajęć z analizą danych neuro, ale już mieli jakiś przedsmak tego, co Was czeka. Oto, co teraz zrobimy:
* zainstalujemy `mne` (jeżeli ktoś nie ma zainstalowanego)
* zaimportujemy z mne funkcję do wczytywania plików typu `.raw` (w takim formacie zapisujemy pliki z naszego Netstation) 
* wczytamy za pomocą tej funkcji dane
* wyświetlimy te dane (otworzymy GUI do przeglądania sygnału)
* przefiltrujemy je (na zajęciach nie przefiltrowaliśmy)
* jeszcze raz wyświetlimy aby zobaczyć zmiany

Sprawdźcie czy macie `mne`:
```python
import mne
```

Instalacja z poziomu konsoli (wymaga gita, ale ściąga najświeższą (developerską) wersję mne):
```
pip install git+https://github.com/mne-tools/mne-python
```
Ale na razie, jeżeli jesteście z własnymi komputerami i nie macie jeszcze `mne`, wystaczy:
```
pip install mne
```

Teraz skorzystamy z modułu `os` aby przejść do folderu z plikami i wylistować je sobie.
```python
os.chdir(r'C:\dane')
fls = os.listdir()
# albo: fls = glob.glob('*.raw')
print(fls[:4])
```

Ok, mamy listę plików, ale jak wczytać dane, które się w nich znajdują? Pliki są w formacie `.raw`, `mne` ma specjalną fuknkcję do wczytywania takich plików - znajduje się ona w module `egi`, który z kolei znajduje się w module `io` (od *input-output*). Funkcja nazywa się`read_raw_egi`. Możemy zaimportować z `mne` tylko tę funkcję w ten sposób:
```python
from mne.io.egi import read_raw_egi
```

Następnie wczytujemy dane:
```python
eeg = read_raw_egi(fls[0], preload=True)
```

Wczytane dane przechowujemy teraz w zmiennej `eeg` - zmienna ta jest jednak specyficznego typu:
```python
type(eeg)
```

Nie będziemy się na razie wkopywać w funkcjonalność obiektów klasy `Raw` (możecie sobie sprawdzić co daje komenda `dir(eeg)`), zwrócimy przede wszyskim uwagę na to, że `Raw` ma metodę ("moc") `plot` pozwalającą wyświetlić dane:
```python
eeg.plot()
```

Niewiele na razie widać, to dlatego że dane nie są automatycznie skalowane, możemy to rozwiązać tak:
```python
sk = dict(eeg=20)
eeg.plot(scalings=sk)
```

Dane nie są przefiltrowane, dlatego niedużo w nich widać. Przefiltrujemy je w związku z tym.
```python
eeg.filter(1, None) # filtr górnoprzepustowy 1Hz
```

Teraz ponownie je wyświetlimy:
```python
eeg.plot()
```

* krótki opis opcji interfejsu do przeglądania danych
* zaznacznie złych kanałów
