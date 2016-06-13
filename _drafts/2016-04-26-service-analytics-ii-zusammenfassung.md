---
layout: post
title:  "Service Analytics B Zusammenfassung SS 2016"
date:   2016-04-26 00:00:00 +0100
categories: posts
---

$$\newcommand{\E}{\mathrm{E}}
\newcommand{\Var}{\mathrm{Var}}
\newcommand{\Cov}{\mathrm{Cov}}$$


# Session 1: Introduction
SA A => Unsupervised, descriptive

SA B => Supervised, predictive

Abschlussarbeit am Institut (z.b. FZI) - 2. Betreuer aus Fakultät Informatik finden

Dozent kommt das erste Mal seit 5 Jahren zur spät zur Vorlesung, weil sein Auto gerammt wird

Übungstermin Di, 1330-1530 bleibt. Materialien werden online gestellt

12.7.2016 Probeklausur

![](analytic-value-escalator.jpg)

# Session 2: Bias–Variance Trade-off

## Notation (Auswahl)

- $$X = x_{ij}$$ ist $$n \times p$$ Matrix (das ist dann wohl der data frame)
- $$n$$ Größe der Stichprobe
    - Index $$i$$
- $$p$$ Anzahl der Variablen/Prädiktoren/Features
    - Index $$j$$
- $$N$$ Größe der Gesamtpopulation
- $$x^j$$ Alle Datenpunkte über 1 Variable (Spaltenvektor aus $$X$$)
- $$x_i$$ 1 Datenpunkt über alle Variablen(transponierter Zeilenvektor aus $$X$$)


## Flexibility and Fitting

- parametrisch: Modell ist keine gute Annährung => schlechte Schätzung
- flexibleres Modell gibt mehr Spielraum
    + aber braucht mehr Parameter => Overfitting
    - So wenige Parameter wie möglich, so viele wie nötig

## Fehler

$$\text{Fehler} = \Var(\hat{f}) + \text{Bias}^2 (\hat{f}) + \Var(\epsilon).$$

Bias
: Fehler ausgehend von falschen Annahmen im Lernalgorithmus. (underfitting)

Variance
: Fehler ausgehend von der Empfindlichkeit auf kleinere Schwankungen in den Trainingsdaten. (overfitting)

*Bias-Variance Trade-Off*
: This is referred to as a
trade-off because it is easy to obtain a method with extremely low bias but
high variance (for instance, by drawing a curve that passes through every
single training observation) or a method with very low variance but high
bias (by fitting a horizontal line to the data). The challenge lies in finding
a method for which both the variance and the squared bias are low. This
trade-off is one of the most important recurring themes in this book.
(aus ISLR)

![](bias-variance.png)

[Gestrichelte Linie in Graphen ist Fehlervarianz (=> besser als das ist Overfitting)]

### Reducible Error

- Wieso quadrierten Fehler?
    + steht in Beziehung zu erklärter Varianz
    + Conditional expectation, also known as the regression function. Thus the
      best prediction of Y at any point X = x is the conditional mean, when best is measured by mean squared error.

- Reducible Error: $$\Var(\hat{f}) + \text{Bias}^2 (\hat{f})$$
    - Distanz zum "echten" Modell
- Irreducible Error: $$\Var (\epsilon)$$
    - nicht beobachtete Variablen oder Rauschen $$\epsilon$$
- => Regelmäßigkeiten in $$\epsilon$$ benutzen, um Model zu verbessern

- (Diskussion) Unterschied zwischen $$Y$$ und $$x^j$$
    - Man zieht einen predictor heraus, den man näher betrachten will. Das ist dann $$Y$$

# Session 3: Sampling

Datum: 3.5.16

- Bias-Variance Tradeoff Beispiel
- Resampling (Holdout, Crossval, Bootstrap)
- 3-way data partitioning


## Resampling

- Holdout method (static split)
    + evtl zu wenig Trainingsdaten, wenn man Testdaten withholdet
    + evtl schlechte Schätzung, wenn doofes Validationset gezogen wurde
- Random Subsampling
- k-fold Cross Validation
    + Je grösser k, desto weniger Variance und desto mehr Bias
    + Extremfall: $$k=1$$ (Leave-One-Out CV (LOOCV))
        * Weniger Bias als (da fast komplettes (n-1 Samples) Trainingsset)
        * Mehr Variance (Viele stark korrelierte Modelle, da nur Unterschied von 1 Sample)
- Bootstrap
    + Random Subsampling mit Zurücklegen
    + Sample aus einem Sample ist evtl. nicht wieder die selbe Verteilung! [=> Also?]
- 3-Way Data Split
    + Mehrere Modelle kombinieren => Wie Gesamtmodell testen?
    + Gesamtmodell-Testset withholden
    + Modelle mit (bspw.) k-fold CV trainieren auf Restdaten
    + Mit dem Withhold-Testset dann Gesamtmodell testen

# Session 4:
