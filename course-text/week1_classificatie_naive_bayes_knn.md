# Week 1 — Classificatie deel 1: Naive Bayes en KNN

## Herhaling ML-basisbegrippen

- **Classificatie** vs **clustering**; **supervised** (begeleid) vs **unsupervised** (onbegeleid).
- **Underfitting / overfitting**.
- **Train/test/validation data**, **cross-validatie**.

## Introductie tot scikit-learn

### Wat is scikit-learn?

Scikit-learn is de **standaard machine learning-bibliotheek** voor Python.
Ze is gebouwd bovenop NumPy en SciPy en biedt een breed scala aan
algoritmes en gereedschappen voor:

- **Classificatie** — o.a. Naive Bayes, KNN, SVM, beslissingsbomen
- **Regressie**
- **Clustering**
- **Dimensionality reduction** (PCA, t-SNE, …)
- **Preprocessing & feature engineering** (normaliseren, one-hot encoding, …)
- **Model selectie & evaluatie** (cross-validatie, hyperparameter tuning, metrics)

### Open source

Scikit-learn is **open source** onder een BSD-licentie — je mag het
gratis gebruiken, ook voor commerciële toepassingen. De bibliotheek wordt
ontwikkeld door INRIA (Frans onderzoeksinstituut) in samenwerking met een
grote community van vrijwilligers. De volledige broncode staat op GitHub
en de documentatie is uitstekend.

### Consistent API-ontwerp: het `.fit`-`.predict`-patroon

Elk algoritme in scikit-learn volgt hetzelfde basispatroon:

1. **Maak het model:** `model = SomeModel(hyperparameter=waarde)`
2. **Train het model:** `model.fit(X, y)` — leer de relatie tussen features en labels
3. **Voorspel:** `model.predict(X_new)` — pas het model toe op nieuwe data
4. **Evalueer:** `model.score(X_test, y_test)` — bereken de nauwkeurigheid

Naast `.fit()` en `.predict()` zijn er ook andere herbruikbare methodes:

| Methode | Gebruik |
|---------|---------|
| `.fit(X, y)` | Train het model |
| `.predict(X)` | Voorspel labels voor nieuwe data |
| `.score(X, y)` | Bereken de nauwkeurigheid van het model |
| `.transform(X)` | Transformeer data (bv. normaliseren) — gebruikt bij preprocessing |
| `.fit_transform(X)` | Fit parameters en transformeer in één stap |

> **Waarom is dit belangrijk?** Omdat je één patroon leert dat voor **alle**
> algoritmes in deze cursus werkt — of het nu Naive Bayes, KNN, of een latere
> classifier is. Dat maakt scikit-learn uitzonderlijk consistent en
> gebruiksvriendelijk.

In de rest van deze cursus zullen we dan ook systematisch met scikit-learn
werken. Telkens als je een nieuw algoritme ziet, herken je het `.fit()`-
en `.predict()`-patroon.


## Naive Bayes
### Historische noot

De regel van Bayes is vernoemd naar de Engelse predikant en wiskundige **Thomas Bayes (1701–1761)**. Zijn beroemde stelling werd pas na zijn dood gepubliceerd in 1763, dankzij zijn vriend Richard Price. Onafhankelijk van Bayes herontdekte en generaliseerde de Franse wiskundige **Pierre-Simon Laplace (1749–1827)** de stelling — vandaar dat we ook vaak spreken van de **Bayes–Laplace-stelling**.

De eerste toepassing van Naive Bayes in een ML-context wordt vaak toegeschreven aan **M.E. Maron (1961)**, die het gebruikte voor automatische indexering van wetenschappelijke artikelen.[^maron1961] In de jaren 1990 en 2000 werd Naive Bayes de *de facto* standaard voor **e-mail spamfiltratie** (Sahami et al., 1998[^sahami1998]; Graham, 2002[^graham2002]).

De term "naief" verwijst naar de expliciete — en bewust onrealistische — aanname dat alle features onafhankelijk zijn. Deze aanname is al sinds de jaren 1960 gekend als een vereenvoudiging, maar in de praktijk presteert de classifier verrassend goed, vandaar dat hij nog steeds veel gebruikt wordt.

[^maron1961]: M.E. Maron, "Automatic Indexing: An Experimental Inquiry", *Journal of the ACM*, vol. 8, no. 3, pp. 404–417, 1961. [doi:10.1145/321075.321084](https://doi.org/10.1145/321075.321084)
[^sahami1998]: M. Sahami, S. Dumais, D. Heckerman, E. Horvitz, "A Bayesian approach to filtering junk e-mail", *AAAI Workshop on Learning for Text Categorization*, 1998. [PDF](https://www.aaai.org/Papers/Workshops/1998/WS-98-05/WS98-05-004.pdf)
[^graham2002]: P. Graham, "A Plan for Spam", 2002. [paulgraham.com/spam.html](https://paulgraham.com/spam.html)

### Conditionele (voorwaardelijke) kans

- $P(A|B)$: de kans op A als B al is gebeurd.
- Definitie: $P(A|B) = \dfrac{P(A \& B)}{P(B)}$
- Voorbeeld: kans op twee keer zes gooien = $1/36 = P(A\&B)$; kans op één keer zes = $1/6 = P(B)$; dus $P(A|B) = \frac{1/36}{1/6} = \frac{1}{6}$.

### Regel van Bayes

$$P(A|B) = \frac{P(B|A)\,P(A)}{P(B)}$$

- In ML-context: B = data/feiten, A = het classificatielabel dat we willen toekennen. We willen dus $P(\text{label} \mid \text{data})$ berekenen.

### Spamfilter voorbeeld

Gegeven: $P(\text{moneytransfer} \mid \text{spam}) = 0.4$, $P(\text{moneytransfer} \mid \neg\text{spam}) = 0.0001$, $P(\text{spam}) = 0.45$.

$$P(\text{spam} \mid \text{mt}) = \frac{P(\text{mt} \mid \text{spam})\,P(\text{spam})}{P(\text{mt}\mid\text{spam})P(\text{spam}) + P(\text{mt}\mid\neg\text{spam})P(\neg\text{spam})}$$

De classifier kiest het label met de grootste kans (hier: spam).

### Meerdere woorden screenen

Met meerdere tokens ($mo$=moneytransfer, $ug$=uganda, $ap$=AP Hogeschool):

$$P(\neg mo \& ug \& ap \mid \text{spam}) = P(\neg mo \mid \text{spam})\,P(ug \mid \text{spam})\,P(ap \mid \text{spam})\cdots$$

- Idem voor $\neg$spam; kies uiteindelijk het label met de grootste teller (noemer hoeft niet uitgerekend te worden).

### Waarom "naief"?

- De kansen van woorden samen en apart zijn niet onafhankelijk — ze beïnvloeden elkaar in werkelijkheid.
- In de praktijk werkt de classifier vrij goed, dus we negeren dit.

### Laplace-smoothing (uit notebook)

Als $P(x_i \mid y=k) = 0$ voor een label, wordt het hele product 0. Oplossing: tel een klein getal $\delta$ op:

$$P(x_i \mid y=k) = \frac{\delta + \#(x_i, y=k)}{\sum_j (\delta + \#(x_j, y=k))}$$

$\delta$ wordt bepaald in functie van de documentgrootte en kan met cross-validatie worden geschat.

### Implementatie (sklearn)

```python
from sklearn.naive_bayes import BernoulliNB
clf = BernoulliNB()
clf.fit(X, Y)
clf.predict(X[2:3])
```

Let op de `.fit`/`.predict`-structuur — dit komt overal terug.

## K-Nearest Neighbours (KNN)

### Historische noot

K-Nearest Neighbours (KNN) is een van de oudste en meest eenvoudige machine learning-algoritmes. Het werd voor het eerst beschreven door **Evelyn Fix en Joseph Hodges (1951)** in een technisch rapport voor de US Air Force School of Aviation Medicine.[^fix1951] Zij stelden de "nearest neighbor"-beslissingsregel voor als een **niet-parametrische** classificatiemethode — een methode die geen aannames maakt over de onderliggende statistische verdeling van de data.

In 1967 publiceerden **Thomas Cover en Peter Hart** het baanbrekende artikel *"Nearest neighbor pattern classification"*,[^cover1967] waarin ze bewezen dat de foutmarge van 1-NN, bij voldoende data, **hoogstens twee keer zo groot is als de Bayes-optimale fout** — een verrassend sterk theoretisch resultaat.

Het concept van **"lazy learning"** werd al vroeg opgemerkt: KNN heeft geen expliciete trainingsfase, het slaat eenvoudigweg alle trainingsdata op en voert pas tijdens de classificatie de eigenlijke "berekening" uit.

Dankzij zijn eenvoud en intuïtieve werking werd KNN vanaf de jaren 1990 breed toegepast in **patroonherkenning, beeldclassificatie** en **aanbevelingssystemen** (collaborative filtering: "gebruikers zoals jij houden ook van…"). Het is nog steeds een relevant algoritme, al wordt het bij grote datasets vaak ingeperkt door de computationele kost van het berekenen van alle afstanden.

[^fix1951]: E. Fix & J.L. Hodges, "Discriminatory Analysis — Nonparametric Discrimination: Consistency Properties", USAF School of Aviation Medicine, Randolph Field, Texas, 1951. Zie ook: [Wikipedia over KNN](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm)
[^cover1967]: T.M. Cover & P.E. Hart, "Nearest neighbor pattern classification", *IEEE Transactions on Information Theory*, vol. 13, no. 1, pp. 21–27, 1967. [doi:10.1109/TIT.1967.1053964](https://doi.org/10.1109/TIT.1967.1053964)

### Basisidee

- Gelijkaardige dingen zijn "dicht bij elkaar".
- Algoritme:
  1. Kies K (aantal buren).
  2. Voor een nieuw punt: bereken de K dichtste datapunten.
  3. Het meest voorkomende label van die K punten is de voorspelling.

### Eigenschappen en aandachtspunten

- Supervised; zowel binary als multiclass.
- Werkt enkel als er een notie van "afstand" is:
  - enkel numerieke data → **normaliseren is verplicht!**
  - of zelf een metriek definiëren voor categorische data (bv. vogelvluchtafstand tussen steden).
- K moet op voorhand gekozen worden.
- Training kost nauwelijks tijd, maar classificatie is traag (veel afstanden berekenen).

- Visueel (k=5): vindt het algoritme 4 × rood en 1 × groen, dan is de voorspelling rood.

### Implementatie (sklearn)

```python
from sklearn.neighbors import KNeighborsClassifier
knn = KNeighborsClassifier(n_neighbors=k)
knn.fit(X_train, y_train)
predictions = knn.predict(X_test)
```

## Belangrijk bij implementatie (Week 1)

- **Normaliseer** je features vóór KNN — afstanden zijn anders vertekend.
- Kies K via cross-validatie, niet "op het gevoel" - hier komen we later nog op terug.
- Gebruik `.fit`/`.predict` via scikit-learn in plaats van zelf te implementeren, tenzij de oefening het vraagt.
- Check dat categorische features een bruikbare metriek hebben of one-hot encoded worden. Op One-Hot encoding komen we later nog terug.

## Kernpunten

- Naive Bayes: Bayes' regel + aanname van onafhankelijke features (Laplace-smoothing tegen nulkansen).
- KNN: lazy learner — geen training, trage predictie, afstandsgebaseerd.
- Beide zijn supervised classifiers; beide vereisen goede datavoorbereiding.