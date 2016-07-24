---
layout: post
title:  "Service Analytics A"
date:   2016-04-26 00:00:00 +0100
categories: posts
---

Gehalten von Prof. Fromm und Prof. Setzer. Work in progress!

# Session 1: Introduction to Service Analytics
- einfach reinlabern erwünscht
- flip teaching will be donnne
- Übung ist mittwochs 1630-1900
- 12.07 Probeklausur

## Wieso Datenanalyse?
- Oft kein direkter Kundenkontakt/-feedback
- Automatische Datensammlung => wertvolle Infos
- Große Datenmengen => Neue Techniken erforderlich

![](../../media/analytic-value-escalator.jpg)

# Session 2: Data Geometry
Das Hervorheben von descriptive und predictive analytics mit Visualisierungen im Datenraum (zB Cluster, classification boundaries, Regressionsgeraden)

## Variablentypen
- Nominal (Kategorien)
- Ordinal (geordnete Kategorien)
- Binär (boolean)
- Numerisch (int/real, interval (kein Nullpunkt), ratio (mit Nullpunkt))
- Time-series
- ...?

Wide Data
: mehr Features als Samples

## Measuring the Central Tendency
- Sample mean: $$\bar X$$
- Population mean: $$\mu$$
- Sample variance: durch $$n-1$$ teilen (Begründung: 1 Freiheitsgrad weniger, da man letztes Samples aus vorherigen berechnen kann)

$$\frac{1}{n-1}\sum_{i=1}^n (x_i - \bar x)^2$$

## Object Space

= Samples als Achsen (anstatt Features)

- dann kann man nicht so viele Samples gleichzeitig visualisieren (>3 wird schwer), aber viele Features

- Sample variance des Variablenvektors $$x$$ ist $$c |x|$$

-  Winkel zwischen zwei Vektoren gibt Pearson-Korrelationskoeffizienten an
    + positiv korreliert: In selbe Richtung zeigend
    + negativ korreliert: In entgegengesetzte Richtung zeigend
    + unkorreliert: senkrecht aufeinander

  $$\cos(\angle(x,y)) = \frac{xy}{|x||y|}=\frac{\sum_i (x_i y_i)}{\sqrt{\sum_i (x_i^2)} \sqrt{\sum_i (y_i^2)}}$$

  d.h.


- (Solo-)Diskussion: Was ist wenn (in 3d) eine von 2 Variablenvektoren aufgespannte Ebene auf einer von 2 Objektachsen aufgespannten Ebene liegt? oderso?

## Similiarity Measures

- Numerisch
	- cosine similiarity
	- Minkowski distance $$\sqrt[h](bla)$$
		- $$h=1$$: Manhatten distance (L1 Norm)
		- $$h=2$$: Euclidian distance (L2 Norm)
		- $$h \to \infty$$: Lmax-Norm (Größte Differenz)
- Nominal: proximity measure (Gucken, wie oft was im Vergleich zu was anderem vorkommt)
- Binär:
	- contingency table
	- symmetric
	- asymmetric (Fall False-False wird rausgeschmissen, z.B. wenn das 	der Normalfall ist und sehr häufig vorkommt)
	- Jaccard coefficient / "coherence"

Simpsons Paradox
:  A paradox in which a trend appears in different groups of data but disappears or reverses when these groups are combined. Ein Grund sind z.B. unerfasste Störfaktoren oder unausgewogene Stichprobengrößen zwischen den Gruppen.

	[Beispiel](https://en.wikipedia.org/wiki/Low_birth-weight_paradox): Untergewichtig geborene Babies haben eine höhere Mortalität. ABER untergewichtige Babies, die von Raucherinnen geboren werden, haben eine niedrigere Mortalität als die von Nicht-Raucherinnen.

	Grund: Andere Faktoren die zu Untergewicht beitragen sind im Mittel schädlicher als Rauchen.

Frage: Automatisch Distanz aus nicht-numerischen Kategorien festlegen? \
Antwort: Variablen gewichten mit domain knowledge™

Ist ein Walfisch (Säugetier) ähnlicher zu Elefant oder Haifisch?



# Session 3: Data Approximation and Relationships

## Curse of Dimensionality
- Meiste Daten in den Ecken
- Alle Vektoren ungefähr gleich lang
- Varianz konvergiert gegen 0


## Object Space
Gast: Prof Fromm

- macht im WS Industrial Services
- 30 Jahre Consultant bei IBM

Objekte sind Achsen und alle Achsen sind aufeinander senkrecht

10 Dimensionen ist ok bei 2 Variablen, man kann sich einfach nur die aufgespannte Ebene anschauen (ist 2D)

- Skript
	- $$s$$ steht für std dev
	- kleine Buchstaben sind zentriert, grosse unzentriert

## Bivariate Linear Regression (in Object Space)

- Variable space: $$\hat{Y} = a + bX.$$ intercept $$a$$
- Object Space: $$\hat{y} = bx$$

Wie Fehler $$e = y - bx$$ minimieren?

Abstand zwischen Regressionsvektor $$\hat{y}$$ und $$y$$ minimieren => $$ex$$ muss 0 sein.

$$b = \frac{xy}{|x|^2} = \frac{\sum_i x_i y_i}{\sum_i x_i^2}$$

s. 24

1 und X sollen orthogonal sein

im Beweis $$x = X - \bar{X}_\text{scalar}$$ einsetzen

orthogonally rearranged:

$$\hat{Y} = (a + b\bar{X}_\text{scalar})\vec{1} + bx$$

dann ist der intercept $$a = \bar{Y} - b \bar{X}$$

2 wichtige Zusammenhänge:

1. Fehler minimieren indem man y verschiebt
2. Umformen in nicht-zentrierte Version

## Goodness of Fit

- gut = Vektoren sind gleich lang (relativ gesehen) = Winkel ist klein (=> Korrelationskoeffizient)
- Winkel ist Korrelation (kleiner = stärker korreliert)
- Länge ist Standardabweichung
- b sagt aus über die Steilheit der Kurve
- Sum of Squares $$SS_y$$
- $$\hat{y} = SS_\text{regression} = SS_\text{total} - SS_\text{residual} = R^2 SS_\text{total}$$
- $$R^2$$ percentage of variance explained

## Multivariate Regression

Regressionsvektor = Linearkombination von erklärenden Variablen

# Session 4: Grouping & Segmentation
- Classification = supervised
- Clustering = unsupervised bla
    - identifies structure
    - reduces data
- Applications:
    - Performance benchmarking (Identify ares of similiar services usage)
    - Marketing (Discover distinct customer bases for targeting)
    - Preprocessing (Compression)
    - Outlier detection
- Good Clustering:
    - **Cohesive within clusters, distinctive between clusters**
    - Works with
        - Arbitrarily shaped clusters
        - Noisy data
        - Arbitrary input order
        - Many dimensions
    - Incremental
    - High Interpretability

## Partitioning approach / Distance-based clustering
Construct various partitions and then evaluate them by some criterion,
e.g., minimize sum of squared distances to partition-centers.


### k-means

```
C = [k Clustermittelpunkte] (zufällig oder nach Heuristik platziert)

while c ändern sich noch signifikant:
    for c in C:
        S = Alle Punkte, für die c der näheste Mittelpunkt ist
        c = Mittelpunkt von S
```

Is sensitive to outliers

=> **k-medoids** (PAM Algorithm)
    - Uses most centrally located object in cluster
    - But: expensive, does not scale

![K-Medoids Algorithmus](../../media/k-medoids.svg)

## Density-based clustering
Based on connectivity and density functions

- Arbitrary cluster shape
- Noise robust
- Useful for outlier detection

### DBSCAN (Density-Based Spatial Clustering of Applications)

- $$\epsilon$$ = maximaler Nachbarschaftsradius
- minPts = Minimale Anzahl Punkte in einer $$\epsilon$$-Nachbarschaft

core point
: A point $$p$$ is a core point if at least minPts points are within distance $$\epsilon$$ of it (including $$p$$). Those points are said to be directly reachable from $$p$$. No points are directly reachable from a non-core point.

density-reachable
: A point $$q$$ is reachable from $$p$$ if there is a path $$p_1, ..., p_n$$ with $$p_1 = p$$ and $$p_n = q$$, where each $$p_{i+1}$$ is directly reachable from $$p_i$$ (so all the points on the path must be core points, with the possible exception of $$q$$).
All points not reachable from any other point are outliers.

    ![](../../media/dbscan-density-reachable.svg)

density-connected
: Two points $$p$$ and $$q$$ are density-connected if there is a point $$o$$ such that both $$p$$ and $$q$$ are density-reachable from $$o$$.

    ![](../../media/dbscan-density-connected.svg)

A cluster then satisfies two properties:

1. All points within the cluster are mutually density-connected.
2. If a point is density-reachable from any point of the cluster, it is part of the cluster as well.

![DBSCAN](../../media/dbscan.svg)

In the diagram, minPts = 4. Point A and the other red points are core points, because the area surrounding these points in an $$\epsilon$$ radius contain at least 4 points (including the point itself). Because they are all reachable from one another, they form a single cluster. Points B and C are not core points, but are reachable from A (via other core points) and thus belong to the cluster as well. Point N is a noise point that is neither a core point nor density-reachable.

#### Algorithm
1. Arbitrary select a point $$p$$

2. Retrieve all points density-connected from $$p$$ w.r.t. $$\epsilon$$ and MinPts

3. If $$p$$ is a core point, a cluster is formed
    - Iterate over all density-connected points and extend cluster where appropriate
    -  If $$p$$ is border point, no more points are reachable from $$p$$

4. Select next (yet untouched) point until all points have been processed

---

---

#### Fuzzy C-Means
Softe Zuteilung von Punkten zu Clustern (1 Gewicht pro Cluster)


#### Semi-supervised Learning
Suppose that we have a set of n observations. For some of the observations,
we have both predictor measurements and a response measurement. For
the remaining observations, we have predictor measurements but no
response measurement.

Such a scenario can arise if the predictors can be measured relatively
cheaply but the corresponding responses are much more expensive to
collect. Examples?

#### Hierarchical Clustering
Weaknesses:

- "Can never undo what was done previously"
- Does not scale well

## Optional: On the relationship of clustering and outlier detection
- Types of outliers: global, local, collective
- density-based outlier detection
- angle-based outlier factors

## Further Considerations for Clustering

- Partitioning criteria
    - Single level vs. hierarchical partitioning

- Hierarchical approach
    - Create a hierarchical decomposition of the objects using some criterion
    - Typical methods: Diana, Agnes, BIRCH, CAMELEON

- Separation of clusters
    - Exclusive (e.g., one customer belongs to only one region) vs. non-exclusive (e.g., one document may belong to more than one class (fuzzy sets, etc.)

We primarily considered single-level, exclusive clustering.



# Data Understanding: Visual Analytics

## Visual Analytics – Key Terms and Concepts

Visual Analytics
: Grand scale: technology that combines the strengths of human and electronic data processing. Examine the actual processes, not just the results.

    - Human: human flexibility, creativity, and background knowledge
    - Machine: enormous storage and processing capacities

![](../../media/visual-analytics.svg)

## Data Sources

Data Warehouse
: A “repository of current and historical data of potential interest to managers throughout the organization”.

    - Subject-oriented
        - Organized by detailed subject (e.g. sales, products, customers)
        - Only relevant data for decision making support
    - Integrated
        - One single repository
        - One single format for data representation
    - Time variant (time series)
        - Contains historical data and allows its daily, weekly, monthly analysis
        - Not necessarily most current status
    - Non volatile
        - Data cannot update the data after it has been entered

Data Lake
: A less organized source of data.

![](../../media/data-lake.png)

## Data Visualization Techniques

- Bar
- Clustered Bar
- Pie
- Mosaic
- Treemap
- Histogram
- KDE
- Boxplot
- QQ
- Radar charts
- Special: Chernoff Faces

### Visualizing correlation

- Heatmap
- Scatterplot matrix
- Wordcloud


## Business Dashboards

- Focus on the user
    - CXO and knowledge worker probably need different information
- Right Metrics and the Right Visuals
    - What metrics do the users need to see?
    - What context does each metric need to make it meaningful? (Target? Variance? Trend? Breakdown by region?)
    - What is the visual representation that best communicates the metric?
- Business Purpose
    - What is the overall business goal of the dashboard?
    - It should easily be possible to answer "why?"-questions within the same interface
- Use Sketches, Mockups and Prototypes
    - Iterate!

Don't use gauges.

- Position of needle does not tell if value is good or bad
- No (historical) context



# Supervised Partitioning





# Anomaly Detection and Robustness





# Guest Lecture





# Feature Reduction and Orthogonalization





# Text Mining and Document Clustering






# Sequential Analysis & Probabilistic Reasoning

Beispiel mit ATM-Platzierung in Hyderabad

- Stadtgebiet in Quadranten einteilen
- Statisiken über Quadranten berechnen (Bushaltestellen, demographische Daten, whatev)
- 26 Features, 10 mit PCA
- PCs kann man je nach einflussnehmender Features beschreiben (z.B. Stadium, Gaming Zones => "Teenager" Cluster)

Wer kauft Bier und Windeln? => Single Väter

- Kellogs Highschool-Sportler sind zu 75% Müsli-Esser => bewerben
- aber Durchschnitt ist 90%...

20 => Anki

# Time Series Analysis

# Prüfung

Verfahren gegenüberstellen, wissen wie sie funktionieren. Kein Definitionen auswendig lernen.

PCA bei komplett unkorrelierten Variablen sinnvoll?

Wann besser Spiderplot oder Barplot?

Wie sind bei einer Regression die Prädiktoren im Raum verteilt?

Was ist Kohärenz? Wie kann man Vektoren vergleichen?

Mindestens 2-3 Aufgaben werden von Daten-Geometrie und Regression handeln


# Wrap-up

16 LARS: Am stärksten korrelierten Prädiktor nehmen und so hoch gewichten, dass der nächste Prädiktor gerade stärker mit der Zielvariable korreliert ist (einen flacheren Winkel hat) usw. [kommt nicht in der Klausur dran]

Cluster analysis, k-Means und DBSCAN: Was passiert wenn ich den Parameter verstelle?

18 Grundsätzlicher Gedanke kennen (inkl Feedback-Loops), Chernov Faces und andere Visualisierungen anschauen. Was führt bei welchen Visualisierungen zu Verzerrungen?

20 Kriterien für margin anschauen (aber kein soft margin, annehmen dass Daten separierbar sind). Was ist purity, impurity?

22 bag-of-words, pro/cons von den Ähnlichkeiten

23 Unit 8 prozone, "nicht so klausurrelevant"


##### Aufgabe 1. Daten-Geometrie (5 Punkte)

Erklären Sie in 2-3 Sätzen: Warum entspricht das Skalarprodukt der
Vektor-Repräsentationen zweier Variablen nicht deren Korrelation, wenn
die Vektoren zuvor nicht auf die Norm von 1 standardisiert wurden?

A: Argumentation über Korrelationformel oder auch: was ist denn wenn ein Vektor sehr groß ist? Noch anders: Wenn wir eine Korrelation zw. 2 Variablen haben dann kann man die Vektoren nach oben skalieren und dabei ändert sich auch die Korrelation.

##### Aufgabe 2. Regressionsräume (7 Punkte)

Sie haben 20 linear unabhängige Variablen mit jeweils 20 Beobachtungen.
Wie hoch ist die Dimensionalität von deren Regressionsraum? Erläutern
Sie kurz: Was ist die Gefahr, wenn Sie mit den 20 Variablen eine weitere
Variable über ein multiples lineares Regressionsmodell erklären wollen?

A: 20 ist die Dimensionalität des Regressionsraums, weil Variablen unabhängig. Gefahr: Die neue Variable ist linear abhängig sein weil mehr Variablen als Beobachtungen ➔ maximales overfitting.

##### Aufgabe 3. Distanzen (8 Punkte)

Angenommen, Sie wollen die Ähnlichkeit zwischen Objekten mit
ausschließlich binär-kodierten Variablen (Geschlecht, Wohnhaft in
Großstadt, hat Produkt X gekauft, ...) vergleichen. Welche Distanzmaße
oder Ähnlichkeitsmaße haben Sie hierfür in der Vorlesung kennengelernt?
Grenzen Sie diese Maße bitte voneinander ab, entweder verbal oder über
eine formale Darstellung.

A: s. Folien

##### Aufgabe 4. Dimensionsreduktion (5 Punkte)

Die Vektor-Repräsentationen von fünf Objekten im Variable-Space liegen
geometrisch auf einer Ebene. Sie führen eine Principal Component
Analyse durch. Wie hoch ist der Anteil der erklärten Varianz durch die 3.,
4., und 5. Komponente? Erläutern Sie Ihre Antwort kurz verbal.

A: Null, weil alle Objekte auf einer Ebene liegen (➔ zwei Dimensionen zum erklären notwendig).

##### Aufgabe 5. Clustering (6 Punkte)

Sie verwenden das Dichte-basierte Clustering-Verfahren DBSCAN in einer
Standard-Konfiguration. Angenommen Sie verdoppeln den Parameter
Epsilon-Umgebung und halbieren gleichzeitig den Parameter MinPoints.
Finden Sie damit dann die gleichen Cluster wie zuvor oder eventuell
andere Cluster? Erläutern Sie bei Ihrer Antwort auch kurz die Bedeutung
der beiden Parameter.

A: Erstmal Parameter erklären. Man bekommt größere Cluster raus [und ich denk mal vereinzelt auch kleinere, wo sonst keine gewesen wären].

##### Aufgabe 6. Klassifikation (7 Punkte)

Sie haben in der Vorlesung Maximum Margin Classifier kennengelernt.
Erläutern Sie, gegebenenfalls auch unterstützt durch eine Skizze: Was
versteht man unter einer Margin, und warum ist es vorteilhaft, diese beim
Lernen eines Classifiers zu maximieren?

A:

##### Aufgabe 7. Text Mining (5 Punkte)

Bitte erläutern Sie: Was versteht man unter der Kosinus-Ähnlichkeiten von
Dokumenten, und wie kann man diese nutzen um Dokumente zu
gruppieren?

A:

##### Aufgabe 8. Entscheidungsbäume (5 Punkte)

Erläutern Sie den Zusammenhang zwischen Entropie und Unsicherheit. In
welcher Beziehung stehen die Begriffe Entropie und Information Gain
beim Lernen von Entscheidungsbäumen? Erläutern Sie Ihre Antwort und
gehen Sie dabei auf die formale Definition von Information Gain ein.

A:

##### Aufgabe 9. Probabilistische Netze (6 Punkte)

Drei Variablen sind jeweils paarweise hoch korreliert, also abhängig. Ist es
möglich, dass zwei der Variablen bedingt unabhängig sind, wenn der Wert
der dritten Variable bekannt ist? Falls ja, welche Hypothese treffen
Bayesian Networks dann über die kausale Beziehung der drei Variablen?
Erläutern Sie Ihre Antwort kurz.

A:

##### Aufgabe 10. Zeitreihenanalyse (6 Punkte)

Was versteht man unter dem Begriff Autokorrelation? Warum spielt
Autokorrelation in der Zeitreihenprognose eine große Rolle und wie kann
Autokorrelation zur Autoregression genutzt werden? Erläutern Sie in Ihrer
Antwort auch den grundsätzlichen Unterschied zwischen zeitlich
strukturierten Daten und solchen ohne Zeitbezug, was die Prognose
zukünftiger Werte angeht.

A:

##### Bonusaufgabe. Probabilistische Netze ( 6 Punkte)

Was versteht man im Kontext von Bayesian Network unter einer
Conditional Probability Table? Warum ist diese Tabelle oftmals sehr viel
kleiner als eine Tabelle mit allen Joint Probabilities, und was verrät uns der
Größenunterschied über die Struktur der Zusammenhänge in den Daten?

A:

Anstatt der Bonusaufgabe hier kommt wahrscheinlich eine zu Basket/Sequence Analysis
