---
layout: post
title:  "Modeling and Analyzing Consumer Behaviour with R (SS 2016, Dr. Dorner)"
date:   2016-04-26 00:00:00 +0100
categories: posts
---

Übung wird verschoben auf Dienstag 17:30

### Was macht das Institut?

- Beispiel: Analyse von Benutzern bei Online-Marktplattformen (sowas wie Ebay), z.B. irrationales Verhalten wegen "Auktionsfieber"
- Beispiel: Smart Grids, um Strom besser zu verteilen
- ...

### Worum geht's in der Vorlesung?

- Beispiel (Guardian): Frauenkommentare werden eher im Sportteil geblockt, Männerkommentare eher im Modeteil
- Beispiel (Shopping): Wie Fake-Rezensionen erkennen?
- Beispiel (GoT): Kundennetzwerke analysieren (wer ist wichtig?)


- Es geht um 3 Themen:
	- Decision Models and Theories
		- Prospect Theory (Kahneman, Tversky and many others)
		- Myopic Loss Aversion (Thaler and many others)
		- Multi-attribute Utility Theory (Keeney, Raiffa and many others)
	- System Design Implications
		- Application: Financial Planning (Looney and Hardin)
		- Application: E-Commerce (Scholz, Dorner and many others)
			- Recommender Systems
			- Sales Prediction (e.g., online social networks)
	- Data Collection, Processing / Analysis
	- Collection:
		- Lab Experiments
		- Real-world Clicks
		- Textual Data
	- Processing / Analysis:
		- Regression Models
		- Clustering
		- Matrix Factorization
		- Conjoint Analysis
		- Markov Chains

### Was ist Wissenschaft?

[hier kommt noch viele Definitionen, aber erstmal gucken ob das in der Prüfung auch so drankommen wird]

- Tätigkeit, Institution, Ergebnis der Tätigkeit [...]

Klassischer Rationalismus
:	- Vernunft + Deduktion = Logisches Schließen
	- Schließen vom Allgemeinen auf das Spezielle

Kritischer Rationalismus
:	- Fallilbilismus: Keine Erkenntnis ist absolut
	- Vermutungswissen: Wahrheit von Hypothesen nur vermutet

Klassischer Empirismus
:	- Beobachtung + Induktion = Schließen vom Speziellen auf das Allgemeine
	- Schließen vom Speziellen aufs Allgemeine

Wissenschaftliche Revolutionen
:	- Vorwissenschaftliche Phase
	- Normale Wissenschaft
	- Beginn der Revolution
	- Paradigmenwechsel

- Ablauf eines Forschungsprojekts (Schritte 1-10) [.....]
- Gute Fragen
	- sind relevant
	- tragen bei



# Introduction to Decision Theory and Decision Support

Wie entscheiden sich Leute und verarbeiten Informationen?

- Informationen beeinflussen Entscheidungsträger
	+ z.B. Appuser <-> UX

User Output: Decision Quality

- Wie messen? (Bsps)
	+ Gewinn/Zielerreichung (wenn man ein Ziel hat)
	+ Zufriedenheit/Bedauern
	+ Zeit (wie viel erreicht in welcher Zeit)

## Decision-Making

### Rational decision-making
- Set of conceivable actions -> consequences
- Mental order of consequence preferences
- Model evaluated under the given constraints = decision
- => Ein Constraint-Optimization Problem
- 40er/50er war das der *hot shit*, aber Dilemma:
	+ Kann man so komplexe Probleme so schnell im Kopf lösen?
	+ Theorie stimmt nicht mit Verhalten überein
	- Konter: St. Petersburg paradox
		- Pot startet mit 2€ und verdoppelt sich mit Kopf. Bei Zahl wird der Pot ausgezahlt. Wieviel zahlen, um spielen zu dürfen?
		- => Erwartungswert wäre unendlich, also alles was man hat.
		- => *Diminishing marginal utility of money* (Geldwert nimmt logarithmisch ab)

### Bounded rationality (Herbert Simon)
- Grenzen bei Formulierung/Lösen komplexer Probleme und Informationsverarbeitung. Also: Mustern in Grenzen finden und deren systematische Effekte auf Entscheidungsfindung finden.
- "As-if" hypothesis (Milton Friedman)
	- So tun, als ob Menschen komplexe Entscheidungen rational verarbeiten können. (Man kann ja auch Fahrrad fahren ohne darüber nachzudenken)

	- Wir können sowieso keine individuellen Präferenzen beobachten
	- Schlechtere Entscheidungsverhalten sterben sowieso über kurz oder lang aus (natural selection), deswegen nur mit bestem Verhalten beschäftigen

- Aspiration adaption (Reinhard Selten)
	- Man sucht kein Optimum, sondern um *good enough*-Alternativen
	- Wenn nichts gefunden wird, dann Aspirationen nach unten adaptieren

- Fazit: outdated

### Expected Utility Theory
- Entscheidungskriterium ist statische Erwartung der individuellen Bewertungen der Ergebnisse
- Axiome: completeness, transitivity, continuity, and independence
- Konter: Allais paradox
	- Wahl zwischen gamble A vs gamble B
	- Wahl zwischen gamble C vs gamble D
	- Wahlen widersprechen sich
	- Form vom Certainty Effect
	- besser: Prospect Theory


### Prospect Theory (Tversky, Kahneman. Nobelpreis 2002)

![](prospect-theory.png)

- Decisions are based on losses and gains, *not* final outcome
- Evaluation with heuristics

![](prospect-theory-value-function.jpg)


Certainty Effect
: People overweight certain outcomes relative to probable outcomes (z.B. Ebay Sofortkaufen vs. Bieten)

Possibility Effect
: People overweight unlikely outcomes

![](certainty-possibility-effect.png)


- Beispiel: Versicherung zahlt 1% aller Beträge nicht aus => Menschen würden 30% weniger Prämien bezahlen

- 	Certainty/Possibility Effect drehen sich um bei negativem outcome

Framing Effect
: Präsentation beeinflusst Entscheidungsprozess (z.B. Asian Disease Experiment: retten vs. sterben)


Bank Teller Experiment
:	- Ist Linda "Bank teller" oder "Bank teller und Feminist"?
	- Kahneman: people use representativeness as heuristic
    - Critics: wording and framing biased
		- Effect still present,  if wording and framing are accounted for

Conjunction fallacy
: a formal fallacy that occurs when it is assumed that specific conditions are more probable than a single general one.


## Effects on Decision Quality

Pareto Efficiency
: Ein Zustand, in dem es nicht möglich ist, eine Eigenschaft zu verbessern, ohne zugleich eine andere verschlechtern zu müssen.

Decoy Effect / Asymmetric Dominance Effect
: Zwei paretoeffiziente Alternativen, wie zu einer beeinflussen? => Dritte einführen die *nudge* in bestimmte Richtung gibt

Monty Hall Paradox
: Menschen sind schlecht mit bedingten Wahrscheinlichkeiten (not perfect Baysesian updaters)



## Looney et al.

#### Issue: Equity Premium Puzzle

- individual investors tend to hold overly conservative portfolios that provide meager payoffs over time
- cannot be explained by any plausible degree of risk aversion
- => investigate investor behaviour
- psychological approach

#### Question: How can a Web-based DSS be designed to help future retirees overcome the tendency to hold  overly conservative portfolios?

- Which cognitive biases are investigated?
	- "Tendency to hold overly conservative portfolios"
	- Certainty Effect
	- Immediacy Effect
	- *Myopic Loss Aversion*

- How are these biases supposed to lead to sub-optimal
decisions?
	- Myopic Loss Aversion
		- loss aversion
			- individuals weigh losses roughly 2.5 times greater than equivalent gain
		- mental accounting
			- frequency of evaluation determines framing
			- frequent evaluations => narrow framing = segregated evaluations
			- non-frequent evaluation => broad framing = aggregated evaluations
		- theoretical explanation for EPP

![](looney-dss.png)

#### Design

How did they design their experiment?

- Why do you think they chose a mixed (between and
within) design?
 	- um besser zu vergleichen?

#### Findings
- Evaluated with *Linear Mixed Modeling* (LMM)
- Control Variables
	- Portfolio risk higher if:
		- male
		- high risk tolerance
		- Good previous performance
	- Unchanged for:
		- Age
		- Investing Experience
- Main Effects
	- portfolio risk higher if:
		- Supporting H1: DSS presented investors with long-term information horizons (instead of short-term)
		- Supporting H2: DSS system restrictiveness was high (not low; evaluation frequency constant at 1 year)
		- Supporting H3: DSS provided suggestive guidance (instead of no guidance)

- What are the design implications?
	- design criteria for DSS
		- information horizon
		- system restrictivness
		- decisional guidance


#### Analysis

Datum: 17.5.16

Typ von Übung springt ein

- s. Folien, sind super

- Confounding factors
	- nested effects (lab specific [?])
	- crossed effects (subject specific)

---

### Gastvortrag über Bachelorarbeit "Mining behavioral patterns in product configurators"

- Paket geschrieben für R
- Theoretische Grundlagen
	- Prospect Theory => ein Referenzpunkt
	- Dual und Tri-reference point theory
		- Failure, Loss, Gain, Success Bereiche
	- Gewichtungsfaktoren für verschiedene Konfigurationsattributen

---

### Nobelpreis Vorträge

24.5.16

- Herbert Simon
	- 9 PhDs
	- bounded rationality
	    - 3 stages
- Maurice Allais
    - früher Phyisiker, nach dem 2. Weltkrieg dann Oekonom
    - Allais-Paradox
    	- Gegenspiele zu Erwartungswertmaximierung
    	- => Psychologische Komponente vorhanden (Certainty-Effekt etc)

# Gastvortag FZI über Segmentation
[Bis Seite 5 verpasst]

- Segmentierung hinsichtlich Kaufverhalten/-sequenzen
- Wieso? Bester Prädiktor
- Nur nach CR aggregieren: Aggregation nicht aussagekräftig
- HMM

![](Bild von S. 6)

- Lift = 2 => doppelt so gut wie Baseline
- Letzter Kauf wichtiger als frühere
- Weighted Productspace Clustering
	- 1 Dimension pro Produkt
	- Exponential decay für frühere Käufe
- out-of-sample evalution = auf unbekannten Trainingsdaten evaluieren
