---
layout: post
title:  "Service Analytics A (SS 2016, Prof. Fromm und Prof. Setzer)"
date:   2016-04-26 00:00:00 +0100
categories: posts
---

Eine Zusammenfassung zur Vorlesung Service Analytics A (SS2016, Prof. Setzer). Work in progress!

# Session 1: Introduction to Service Analytics
- einfach reinlabern erwünscht
- flip teaching will be donnne
- Übung ist mittwochs 1630-1900
- 12.07 Probeklausur

## Wieso Datenanalyse?
- Oft kein direkter Kundenkontakt/-feedback
- Automatische Datensammlung => wertvolle Infos
- Große Datenmengen => Neue Techniken erforderlich

![](analytic-value-escalator.jpg)

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
- Classification:
    - supervised
- Clustering:
    - unsupervised
    - identifies structure
    - reduces data
- Applications:
    - Performance benchmarking: Identify ares of similiar services usage
    - Marketing: Discover distinct customer bases for targeting
    - Preprocessing: Compression
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
e.g., minimizing the sum of squared distances to partition-centers

S 15

### k-means

1. Initialize k means (e.g. randomly)
2. do until clusters don't change:
    b. Assign each object to nearest mean
    a. Update k means = centroids of clusters

- Sensitive to outliers
    - => k-medoids (PAM Algorithm)
        - Uses most centrally located object in cluster
        - Does not scale

## Density-based clustering
Based on connectivity and density functions

Discover clusters of arbitrary shape
Handle noise
Useful for outlier detection

### DBSCAN (Density-Based Spatial Clustering of Applications)

## Fuzzy clustering

## Semisupervised, hierarchical clustering

## Hierarchical Clustering



# Data Understanding: Visual Analytics

## Visual Analytics – Key Terms and Concepts

Visual Analytics
: Grand scale: technology that combines the strengths of human and electronic data processing. Examine the actual processes, not just the results.

    - Human: human flexibility, creativity, and background knowledge
    - Machine: enormous storage and processing capacities

## Technologies for Visual Analytics

## Data Visualization Techniques

## Visual Analytics in a Business Context




# Supervised Partitioning





# Anomaly Detection and Robustness





# Guest Lecture





# Feature Reduction and Orthogonalization





# Text Mining and Document Clustering





# Sequential Analysis & Probabilistic Reasoning





# Time Series Analysis

