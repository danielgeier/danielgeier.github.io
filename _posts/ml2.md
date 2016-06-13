\[
\newcommand{\argmax}{\operatorname*{arg\,max}}
\newcommand{\argmin}{\operatorname*{arg\,min}}
\]

Meine Zusammenfassung. Ohne Gewähr etcpp, ist unvollständig und frisst deine Hausaufgaben. Fehler gefunden? <daniel@danielgeier.com>

Beim Zusammenschreiben habe ich mich an [Martin Thoma’s](https://martin-thoma.com/machine-learning-1-course) und [honnels](https://blog.honnel.de/maschinelles-lernen-1-zusammenfassung/) Zusammenfassung vergriffen. Danke!

Einführung
==========

Maschinelles Lernen  
Ein System lernt aus Erfahrung E in Hinblick auf eine Klasse von Aufgaben T und einem Performanzmaß P, wenn seine Leistungen bei Aufgaben aus T gemessen mit P durch Erfahrung aus E steigt.

Beispiel: Lernen, Schach zu spielen

-   T = Schachspielen
-   P = Prozent der gewonnenen Spiele
-   E = Spiele gegen sich selbst

Wissensrepräsention  
Wie kann Wissen/Hypothesen repräsentiert werden? ⇒ Entscheidungsbäume, Formale Grammatiken, Aussagenlogik, …

![Komponenten eines lernendes System](media/intro-system.png)

![Lernen als Prozess](media/intro-lernprozess.png)

Einordnungskriterien von Lernverfahren
--------------------------------------

### Typ der Inferenz

Induktiv  
Aus Trainingsbeispielen auf ein allgemeines, dahinter liegendes Konzept schließen (Version Space- Algrotithmus)

Deduktiv  
Vorhandenes generelles Wissen wird eingesetzt um spezielle Regeln für Trainingsbeispiele zu bestimmen (Erklärungsbasierte Generalisierung)

### Ebene des Lernens

Symbolisch  
Ein Teil der Semantik der Daten erkennbar und Lernverfahren macht davon explizit Gebrauch (Entscheidungsbäume)

Subsymbolisch  
Lernverfahren fasst Daten als Signale auf, z. Bsp. Reelle Zahlen (Neuronale Netze)

### Lernvorgang

Überwacht  
Trainingsbeispiele haben bereits zugehörige Klassen annotiert (k-Nearest Neighbor)

Unüberwacht  
Ohne Klassenzugehörigkeit (k-Means)

### Beispielgebung

inkrementell  
Trainingsbeispielle können einzeln in das System eingegeben werden und das System verfeinert mit jedem Beispiel seine Hypothese (bereits eingelernte Trainingsdaten müssen dafür nicht nochmal betrachtet werden (ID5R)

nicht inkrementell  
Hinzunahme zusätzlicher Trainingsbeispiele erneut die ursprünglichen mitverarbeiten (ID3)

### Umfang der Beispiel

umfangreich  
vier/fünfstellige Zahlen (Neuronale Netze)

gering  
eine Hand voll Beispiele (Case-Based-Reasoning)

### Hintergrundwissen

empirisch  
statistisch ausgewertetes Erfahrungswissen (SVMs)

axiomatisch  
logischer Unterbau des Lernverfahrens mit echten formalen logischen Ableitungen (Erklärungsbasierte Generalisierung)

![](media/image1.png)

Die Kriterien sind in folgender Tabelle nochmals für alle Algorithmen zusammengefasst:

|               | ind | ded |     | symb | subsymb |     |  üb | unüb |     | inkr | n. inkr |     | viel | gering |     | emp | axio |
|---------------|:---:|:---:|-----|:----:|:-------:|-----|:---:|:----:|-----|:----:|:-------:|-----|:----:|:------:|-----|:---:|:----:|
| Version Space |  x  |     |     |   x  |         |     |  x  |      |     |   x  |         |     |   x  |        |     |  x  |      |
| NN            |  x  |     |     |      |    x    |     |  x  |      |     |      |    x    |     |   x  |        |     |  x  |      |
| SVM           |  x  |     |     |      |    x    |     |  x  |      |     |      |    x    |     |   x  |        |     |  x  |      |
| k-means       |  x  |     |     |      |    x    |     |     |   x  |     |      |    x    |     |   x  |        |     |  x  |      |
| Agglom H. C.  |  x  |     |     |      |    x    |     |     |   x  |     |      |    x    |     |   x  |        |     |  x  |      |
| COBWEB        |  x  |     |     |   x  |         |     |     |   x  |     |   x  |         |     |      |    x   |     |  x  |      |
| Bayes         |  x  |     |     |      |    x    |     |  x  |      |     |      |    x    |     |   x  |        |     |  x  |      |
| ID3           |  x  |     |     |   x  |         |     |  x  |      |     |      |    x    |     |   x  |        |     |  x  |      |
| C4.5          |  x  |     |     |   x  |         |     |  x  |      |     |      |    x    |     |   x  |        |     |  x  |      |
| ID5R          |  x  |     |     |   x  |         |     |  x  |      |     |   x  |         |     |   x  |        |     |  x  |      |
| Random Forest |  x  |     |     |   x  |         |     |  x  |      |     |      |    x    |     |   x  |        |     |  x  |      |
| HMM           |  x  |     |     |      |    x    |     |  x  |      |     |      |    x    |     |   x  |        |     |  x  |      |
| k-NN          |  x  |     |     |      |    x    |     |  x  |      |     |      |    x    |     |   x  |        |     |  x  |      |
| CBR           |  x  |     |     |   x  |         |     |  x  |      |     |   x  |         |     |      |    x   |     |  x  |      |
| EBL           |     |  x  |     |   x  |         |     |  x  |      |     |   x  |         |     |      |    x   |     |     |   x  |

nope: |NN Auto-Encoder|x | | | |x | | |x | | |x | | |x | |x | |

Induktives Lernen (Konzeptlernen)
=================================

Induktion  
Trainingsbeispiele ⇒ Hypothese

Deduktion  
Vorhandes Hintergrundwissen + Neuformulierung von Wissen ⇒ Hypothese

Induktive Lernhypothese  
“Jede Hypothese, die die Zielfunktion über einer genügend großen Menge von Trainingsbeispielen gut genug approximiert, wird die Zielfunktion auch über unbekannten Beispielen gut approximieren.”

Konzeptlernen  
„Schließen auf eine booleanwertige Funktion aus Trainingsbeispielen ihrer Inputs und Outputs.“ Ziel: Hypothese \(h\) finden mit \(h(x) = c(x) \ \ \forall x \in D \subset X.\) Also die Hypothese stimmt mit dem Konzept von jedem Trainingsbeispiel überein. \(X\) ist der Instanzraum.

Konzept  
Beschreibt eine Untermenge von Objekten/Ereignissen, z.B: \[ \text{vogel} : \text{Tiere} \to { \text{true}, \text{false}\\}, \] \[ \text{vogel}(\text{Hase}) = \text{false}. \]

Spezifität  
Je spezifischer Die Hypothese h, desto weniger Trainingsbeispiele erfüllen h.

Konsistenz  
Keine negativen Beispiele werden positiv klassifiziert.

Vollständigkeit  
Alle positiven Beispiele werden als positiv klassifiziert.

![vollständig vs konsistent](media/image2.png)

Lernen
------

### Specific-To-General-Suche

    h = spezifischste Hypothese
    für jedes unerfüllte positive Trainingsbeispiel:
        erweitere h

Wird benutzt bei Lernen von *Präzedenzgraphen*, vgl. Untertasse-Tasse-Teller.

#### Beurteilung

-   Hypothesenraum beschrieben durch Konjunktionen von Attributeinschränkungen STG liefert spezifischste vollständige Hypothese
-   Falls Trainingsbeispiele korrekt und Zielhypothese in H enthalten Endhypothese auch konsistent
-   Sind Trainingsbsp. konsistent?
    -   Wenn es echte Daten sind, wohl nicht…
-   Endkonzept = korrektes Zielkonzept?
    -   Bsp. faules Obst. Hier ist die Farbe wichtig, aber wenn man nur die Form/whatever als Attribute hat, kommt auch kein gutes Endkonzept raus.
-   Warum spezifischste Hypothese?
    -   Man will oft was eher Generelles ⇒ Version Space

### (General-To-Specific-Suche)

    h = generellste Hypothese
    für jedes erfüllte negative Trainingsbeispiel:
        enge h ein

### Version Space

„Der Versionsraum \(VS_{H,D}\) bezüglich des Hypothesenraums H und der Menge von Trainingsbeispielen D ist die Untermenge der Hypothesen von H, die mit den Trainingsbeispielen in D konsistent ist.“

#### Candidate Elimination Algorithm

    S = {#} // Spezifischste Hypothese
    G = {?} // Generellste Hypothese

    if n == negatives Beispiel:
        Lösche aus S die Hypothesen, die n abdecken.

        Spezialisiere alle Hypothesen in G, bis sie:
            - n nicht abdecken
            - allgemeiner als eine Hypothese in S bleiben.

        Lösche aus G alle Hypothesen, die spezifischer als eine andere Hypothese aus G sind.

    if p == positives Beispiel:
        Lösche aus G die mit p inkonsistenten Hypothesen.

        Verallgemeinere alle Hypothesen in S, bis sie:
            - p abdecken
            - spezifischer als eine Hypothese in G bleiben.

        Lösche aus S alle Hypothesen, die allgemeiner
        als eine andere Hypothese aus S sind.

#### Beurteilung

Konvergiert zur korrekten Hypothese (falls Beispiele konsistent und Hypothese im Hypothesenraum) ![](media/image3.png)

Induktiver Bias
---------------

Grundlegende Eigenschaft von induktiver Inferenz  
„Ein induktives Lernsystem, das keine a priori-Annahmen über die Identität des Zielkonzepts macht, hat keine rationale Basis, um unbekannte Instanzen zu klassifizieren.“

### Hypothesenraumbias

Raum der Hypothesen beschränken. Sehr gute Klassifikation i.A. durch eine komplexe Hypothese erreicht. Aber: Overfitting!

Beispiele:

-   logische Konjunktionen

-   lineare Schwellwertfunktionen

-   Geraden, Polynome …etc

-   3-NN (Nearest Neighbour)

### Präferenzbias

Bevorzugung von Hypothesen. Wähle das \(h \in H\), das möglichst viele Beispiele richtig klassifiziert. Misklassifikation muss in Kauf genommen werden.

Beispiele:

-   Bevorzuge Hypothesen mit weniger Disjunktionen
-   Bevorzuge kleinere Entscheidungsbäume

Reinforcement Learning
======================

Markov Decision Process
-----------------------

5-Tupel aus \(S,A,P,R,\gamma\):

\(S\)  
Endliche Zustandsmenge (states).

\(A(s)\)  
Die Menge von möglichen Aktionen im Zustand \(s\).

\(P\left( s, s', a \right) = P\left( s_{t + 1} = s' | s_{t} = s, a_{t}= a \right)\)  
Die Wahrscheinlichkeit im Zeitschritt \(t + 1\) im Zustand \(s'\) zu sein, wenn man zum Zeitpunkt \(t\) im Zustand \(s\) ist und die Aktion \(a\) ausführt.

\(R\left( s,\ s',\ a \right) \in \mathbb{R}\)  
Die direkte Belohnung, wenn durch die Aktion \(a\) vom Zustand \(s\) in den Zustand \(s'\) gekommen ist.

\(\gamma \in [0,1]\)  
Der Diskontierungsfaktor, welche die Bedeutung von direkten Belohnungen im Vergleich zu künftigen Belohnungen anzeigt. (Klarmachen was \(\gamma = 0\) und \(\gamma \geq 1\) bewirken.)

Außerdem gilt die *Markov-Bedingung*: Der aktuelle Zustand ist nur vom vorherigen abhängig.

Zustandsänderungen werden beschrieben durch die Funktion \(\delta\): \[\delta : (S \times A) \to S\]\[\delta(s_t, a_t) = s_{t+1}\]

Strategielernen - Policy Learning
---------------------------------

Policy  
Eine Policy \(\pi: S \rightarrow\) ist die Vorschrift, in welchem Zustand welche Aktion ausgeführt werden soll.

Policy Learning  
Unter Policy Learning versteht man die Suche nach einer optimalen Policy \(\pi^*\).

Value-Funktion  
Die Funktion \(V^\pi: S \rightarrow \mathbb{R}\) heißt Value-Funktion. Sie gibt den erwarteten Wert (nicht die Belohnung, da bei der V-Funktion noch \(\gamma\) eingeht) eines Zustands \(s\) unter der Policy \(\pi\) an. Mit \(V^*\) wird der Wert unter der optimalen Policy bezeichnet.

### Simple Value Iteration

Schätze \(V(s)\) durch \(\hat{V}^*(s)\) (bis Konvergenz): \[\hat{V}^*(s_t) \leftarrow r_t + \gamma \hat{V}^*(s_{t+1})\]

### Simple Temporal Difference Learning

Wie Simple Value Iteration, aber mit Lernrate \(\alpha\). \[\hat{V}^*(s_t) \leftarrow (1-\alpha) \cdot \hat{V}^*(s_t) + \alpha(r_t + \gamma \hat{V}^*(s_{t+1}))\]

### Q-Lernalgorithmus

Q-Funktion  
Die Funktion \(Q^\pi : S \times A \to \mathbb{R}\) gibt den erwarteten Wert einer eines Zustandes \(s\) unter der Policy \(\pi\), wenn die Aktion \(a\) ausgeführt wird an. Es gilt: \(Q^\pi(s, \pi(s)) = V^\pi(s)\)

\(Q\) wird anfangs mit 0 initialisiert. Beim Training wird die Folgeaktion \(s_\text{next}\) mit dem höchsten \(Q\)-Wert ausgewählt und \(Q\) dementsprechend angepasst. \[Q(s,a) \verb| += | r(s,a) + \gamma\max\limits_{a'} Q(s_\text{next}, a')\]

#### Probabilistische Auswahl von Aktionen

Welche Aktion wählen? ⇒ Exploration vs. Exploitation

Suchstrategie im Laufe des Lernprozesses von global (neue Aktionen) nach lokal (bekannte Aktionen) zu bewegen. Dazu gibt es einen Parameter \(k > 0\). Kleines \(k\) = global, großes \(k\) = lokal. \[P(a_i|s) = \frac{k^{\hat{Q}(s, a_i)}}{\sum_i k^{\hat{Q}(s, a_i)}}\]

#### Optimierungen

-   In jeder Lernepisode alle \(Q(s,a)\) anpassen
-   Änderungen zu abhängigen \(Q\)-Werten propagieren
-   Mit adaptivem Model lernen, d.h. meiste Aktionen in simulierter Umgebung ausführen, einige wenige Aktionen in realer Umgebung. Dabei das Modell anpassen.

#### Generalisierung

kontinuierlicher Zustandsraum ⇒ \(Q\)-Tabelle zu groß, zu viele Iterationen

Lösung: Kombination mit Methoden höherer Generalisierung. z.B.: Neuronale Netze

#### Nichtdeterministische MDP

Folgezustand tritt entsprechend einer Verteilung auf ⇒ was machen?

Lösung: Erwartungswert als Bewertung.

Lernen von Aktionssequenzen
---------------------------

Reward erst nach Aktionensequenz oder sogar erst ganz am Ende bekannt (z.B. Schach Win/Lose).

### TD(\(\lambda\))-Lernen

Eligibility Traces  
Speichern zeitliche Informationen, z.B. frühere Zustände.

Vorwärtssicht  
gewichtete Anpassung anhand den \(n\) nachfolgenden Schätzungen.

![Vorwärtssicht (theoretisch)](media/tdl.png)

Rückwärtssicht  
gewichtete Fehlersignale (*temporal differences*) in Schätzungen nach hinten weitergeben.

![Rückwärtssicht (praktisch)](media/tdl2.png)

#### SARSA(\(\lambda\))-Algorithmus mit Eligibility Traces

Ist anscheinend Q-Learning mit Eligibility Traces. Algorithmus: meh.

Lerntheorie, Algorithmenunabhängige Verfahren (für überwachtes induktives Lernen)
=================================================================================

Allgemeines
-----------

Occam’s Razor  
Die einfachste Theorie ist allen anderen vorzuziehen.

Lernmaschine  
Besteht aus

-   Hypothesenraum
-   Lernverfahren (um die optimale Hypothese \(h_\text{supi}\) zu finden)
-   Modell (gegeben durch Auswertung von \(h_\text{supi}\))

### Probleme beim Lernen

-   statistisch: Zu großer Hypothesenraum ⇒ mehrere Hypothesen eignen sich gleich gut

-   Komplexität: Problem zu komplex, Lernverfahren findet u.U. keine optimale Lösung

-   Repräsentation: Hypothesenraum enthält keine gute Approximation der Zielfunktion

------------------------------------------------------------------------

CUT von Stoff den ich schon kenne (Fehlerfunktion ⇒ Lern-, Verifikations- und Generalisierungfehler, Distanzfunktionen, Gradientenabstieg, Overfitting, Modellwahl/-güte (vgl. occam’s razor), cross validation)

------------------------------------------------------------------------

Bagging & Boosting
------------------

Siehe auch [Bagging, boosting and stacking in machine learning](https://stats.stackexchange.com/questions/18891/bagging-boosting-and-stacking-in-machine-learning).

### Bootstrap

Wiederholtes zufälliges Sampling (Zufallsstichprobe) aus dem Sample das man hat.

### Bagging (**B**ootstrap **agg**regat**ing**)

1.  Bootstrap ⇒ mehrere Samples
2.  für jedes Sample eigenes Modell
3.  Modelle kombinieren

### Boosting

Mehrere *weak classifier* werden sequentiell auf wiederholt modifizierten Varianten der Daten trainiert. Diese werden dann durch *majority voting* zu einem starken Klassifikator kombiniert.

Der Originalalgorithmus:

-   Zerlege \(D\) in mehrere Datensätze, z.B. in \(D_1, D_2, D_3\)
-   Wähle \(D_1\) und bestimme das Modell \(M_1\)
-   Wähle für \(D_2\) aus \(D\) neue Beispiele s.d. 50% durch \(M_1\) korrekt geschätzt werden und erstelle damit \(M_2\)
-   Wähle für \(D_3\) Beispiele bei denen \(M_1\) und \(M_2\) gegensätzlich sind und bestimme \(M_3\)
-   Kombiniere die Modelle: \(M = \begin{cases} M_1, & M_1 = M_2 \\ M_3, & \text{sonst} \end{cases}\)

#### AdaBoost (Adaptive Boosting)

``` python
# Alle Datenpunkte gleich gewichtet
gewichte = {1, ..., 1}

for i in range():
    Trainiere Klassifikator i mit gewichteten Daten
    for x in X:
        w = zugehöriges Gewicht
        if x richtig klassifiziert:
            w verkleinern
        else:
            w vergrößern
```

Die *Viola-Jones Gesichtserkennung* verwendet AdaBoost. Zusätzlich wird Kaskadierung verwendet, d.h. es wird erst ein schneller schlechter Klassifikator mit hoher FP-Rate verwendet und nur weiter klassifiziert, falls dieser positiv klassifiziert.

Probably Approximate Correct (PAC)
----------------------------------

-   Wir wollen eine *approximate correct* \(\epsilon\)-genaue Hypothese finden mit \(E_D(h) \leq \epsilon, 0 < \epsilon < 0.5\).

-   Diese wird mit Wahrscheinlichkeit \(1 - \delta, 0 < \delta < 0.5\) gefunden.

-   Das geschieht in polynomialer Zeit abhängig von \(\frac{1}{\delta}, \frac{1}{\epsilon}, n\) mit Speicheraufwand abhängig vom Konzept \(C\).

Stichprobenkomplexität  
Anzahl benötiger Lerndaten \(m\). Es gilt \[m \geq \frac{1}{\epsilon}\left(\ln\left(\frac{1}{\delta}\right) + \ln|H|\right).\] D.h. je größer der Hypothesenraum, je größer die Sicherheit und je kleiner der Fehler, umso mehr Daten werden benötigt.

Zum Beispiel:

-   Hypothesen aus Konjunktionen mit bis zu 10 Literalen.
-   zu 95% Sicherheit Hypothese gefunden
-   Fehler &lt; 0.1

\[\implies m \geq \frac{1}{0.1}\left(\ln\left(\frac{1}{0.05}\right) + \ln|3^{10}|\right) \approx 140.\]

VC-Dimension (Vapnik-Chervonenkis)
----------------------------------

Sagt aus, wieviele Punkte (aus 2 Klassen) eine Hypothese (Klassifikator) maximal separieren kann.

![2D-Geraden haben eine VC-Dimension von 3.](media/vc.png)

Nun kann man den Testfehler genauer abschätzen als mit PAC. \[E_{Train} \leq E_{Test} + \sqrt{\frac{VC}{\text{\# Lernbeispiele}} + \cdots}\]

### Structural Risk Minimization

Strukturiere den Hypothesenraum nach VC-Dimension und wähle so, dass der Fehler minimiert wird. Das geltende Prinzip hier “so niedrig wie möglich aber so hoch wie nötig”, denn höhere VC-Dimensionen führen zu Overfitting.

Neuronale Netze
===============

Perzeptron
----------

![Geometrische Interpretation der Gewichte als Hyperebene](media/nn-perceptron.png)

### Lernalgorithmus

Ausm Skript:

    Start:
        Gegeben Lerndatenmenge P ∪ N
        Der Gewichtsvektor w(0) wird zufällig generiert.
        Setze t := 0.
    Testen:
        Ein Punkt x in P ∪ N wird zufällig gewählt.
        Falls x ∈ P und w(t)x > 0 gehe zu Testen
        Falls x ∈ P und w(t)x ≤ 0 gehe zu Addieren
        Falls x ∈ N und w(t)x < 0 gehe zu Testen
        Falls x ∈ N und w(t)x ≥ 0 gehe zu Subtrahieren
    Addieren:
        Setze w(t+1) = w(t) + x.
        Setze t := t+1. Gehe zu Testen.
    Subtrahieren:
        Setze w(t+1) = w(t) - x.
        Setze t := t+1. Gehe zu Testen.

Multilayer Perceptron
---------------------

### Gradientenabstieg

\[C = \frac{1}{2n} \sum_x \|y(x)-a^L(x)\|^2\]

Die Aktivierungsfunktion \(a^L\) ist oft die *Sigmoidfunktion*: \[\begin{aligned}
f(x) &= \frac{1}{1 + e^{-x}}, \\
f'(x) &= f(x) (1 - f(x)).
\end{aligned}\]

Anpassung von \(w\) durch \(w \leftarrow w + \eta \nabla E(w).\)

### Backpropagation

[How the backpropagation algorithm works](http://neuralnetworksanddeeplearning.com/chap2.html).

Bei der Herleitung benutzt man dann ganz oft die Kettenregel.

Radial Basis Function Netze (RBF)
---------------------------------

Benutzen in der hidden layer radialsymmetrische Basisfunktionen, z.B. Gaussfunktion.

Konstruktive Lernverfahren (Schrittweises Vergrößern)
-----------------------------------------------------

Cascade Correlation  
Netz ohne hidden layer initialisiert. Wenn der Fehler nicht mehr kleiner wird, neues *candidate neuron* einfügen und einzeln trainieren. Und von vorne!

Dynamic Decay Adjustment (DDA)  
Konstrukives Verfahren für RBF-Netze

Optimierungen für NN
--------------------

-   Momentum Term
-   Normierung der Schrittweite
-   RPROP (Lernratenanpassung)
-   Verbesserung der Generalisierung: z.B Optimal Brain Damage

Support Vector Machine (SVM)
============================

(Alle \(x, y, w\) hier sind Vektoren.)

Gesucht: Hyperebene \(wx + b = 0\), die Klassen trennt. Dazu muss \(\lVert w \rVert^2\) minimiert werden ⇒ Lagrange-Multiplikatoren \(\alpha_1, \dots, \alpha_n\). Die Datenpunkte für die \(\alpha_i \neq 0\) heißen *support vectors*.

Soft Margin
-----------

Erlaube geringe Anzahl Missklassifikationen. Regulierungsparameter \(C\): Maß, wie viel Abweichung vom Margin erlaubt ist.

> If \(C\) is close to \(0\), then we don’t pay that much for points violating the margin constraint. We can minimize the cost function by setting \(w\) to be a small vector - this is equivalent to creating a very wide “tube” or safety margin around the decision boundary (but having many points violate this safety margin - see Figure 11.1.1). If \(C\) is close to \(\infty\), then we pay a lot for points that violate the margin constraint, and we are close the hard-margin formulation we previously described - the difficulty here is that we may be sensitive to outlier points in the training data.
> \[[Quelle](https://www.ics.uci.edu/~dramanan/teaching/ics273a_winter08/lectures/lecture11.pdf)\]

Kernel Trick
------------

Idee: Transformiere Daten in anderen Raum, wo sie besser separierbar sind.

z.B. Monomtransformation \[\Phi : \mathbb{R}^2 \to \mathbb{R}^3 \text{ mit } (x_1x_2) \mapsto (x_1^2, \sqrt{2} x_1 x_2, x_2^2).\] ![Monotransformation](media/svm-monom.png)

Transformation zeitaufwendig und in Berechnung sowieso nur Skalarprodukt verwendet ⇒ Berechne das Skalarprodukt im transformierten Raum durch einen *Kernel*. \[K(x,y) = \Phi(x) \Phi(y)\]

z.B. Kernel zur Monomtransformation \[\begin{aligned}
\Phi(x) \Phi(y) &= (x_1^2, \sqrt{2} x_1 x_2, x_2^2) (y_1^2, \sqrt{2} y_1 y_2, y_2^2) \\
&= (xy)^2 \\
&:= K(x,y). \end{aligned}\]

### Liste von Kernelfunktionen

-   \(xy\) (Skalarprodukt)
-   \((xy+c)^d\) (Polynomial)
-   \(\exp\left(-\frac{\lVert x-y \rVert ^2}{2 \sigma^2}\right)\) (RBF)
-   \(\tanh (\kappa(xy) + \theta)\) (Sigmoid)

Version Space Duality
---------------------

Es folgen fesche Begründungen dafür wieso SVMs nicht nur praktisch, sondern auch theoretisch toll™ sind.

![](media/svm-duality.png)

![](media/svm-duality2.png)

![Erinnerung Structural Risk Minimization (SRM): VC-Dimension soll nicht zu niedrig, aber auch nicht zu hoch sein](media/svm-duality3.png)

Erweiterungen
-------------

### k-Klassen-Klassifizierung

-   one vs. all
-   one vs one
-   multiple
-   k-class SVM von Waktins

### Gewichtete SVM

je ein C für positive und negative Klassen

### Dichte-Träger Schätzung

so was wie Clustering mit SVMs

Pro/Kontra
----------

Pro:

-   Optimale VC-Dimension ⇒ korrektes Lernen
-   Verarbeitung hochdimensionaler Daten
-   Schnelle Auswertung
-   …

Contra:

-   Vorverarbeitung extern (vgl. deep learning)
-   Finden des Kernels und der Parameter
-   Trainingsaufwand
-   …

Unüberwachte Lernverfahren
==========================

k-Means Clustering
------------------

``` python
C = [k Clustermittelpunkte] (zufällig oder nach Heuristik platziert)

while c ändern sich noch signifikant:
    for c in C:
        S = Alle Punkte, für die c der näheste Mittelpunkt ist
        c = Mittelpunkt von S
```

### Pro/Kontra

-   Resultate hängen stark von Initialisierung ab
    -   Algorithmus mit verschiedenen Initialisierungen ausführen
    -   Beim k muss man rumprobieren, wenn kein Domänenwissen ⇒ Overfitting
-   Resultate hängen von Metrik \(|x - c_j|\) ab
    -   Viele Dimensionen ⇒ Alle Daten unähnlich (curse of dimensionality)

Fuzzy k-Means Clustering
------------------------

\(x\) haben variable Zugehörigkeit zwischen 0 und 1 zu allen Clustern: \[P(c_j|x_i) = \frac{\left(\frac{1}{d_{ij}}\right)^{\frac{1}{b-1}}}{\sum_{r=1}^k \left(\frac{1}{d_{ir}}\right)^{\frac{1}{b-1}}} \text{ mit } d_{ij} = |x_i - c_j|^2.\] (wat)

Agglomerative Hierarchical Clustering
-------------------------------------

Hat mehr Struktur als k-means. Ballungen bestehen wieder aus Ballungen.

``` python
t = Minimalabstand den Cluster haben müssen
D = [[x] for x in X]
c = 0

while (c < maximale Anzahl Schritte) und (die minimale Distanz < t):
    D_i, D_j = die zwei Cluster aus D mit der minimalen Distanz
    D.ersetze(D_i und D_j durch merge(D_i, D_j))
    c++
```

### Pro/Kontra

-   simpel
-   besser als k-means, wenn k unbekannt, aber man ne Idee hat wie man d und t sinnvoll wählt.
-   Unabhängig von Initialisierung
-   \(O(n^3)\)
-   Rauschanfällig

Begriffliche Ballung
--------------------

Die Verfahren oben sind *klassische* Ballungsverfahren deren Ähnlichkeitsmaß kontextfrei ist. COBWEB hingegen ist ein *begriffliches* Ballungsverfahren, dass konzeptuelle Zusammenhänge einbezieht. Das war in den 90ern (als die Folien geschrieben wurden) gerade total in.

### COBWEB

Lernen durch inkrementelles Aufbauen und Anpassen eines Strukturbaums. Nominale Attributwerte sind ok.

#### Grundprinzip

-   Repräsentation: Begriffshierarchie als Baum
-   Maximiere Ähnlichkeit in Klassen
-   Maximiere Unterschiede zwischen Klassen
-   Die Distanzfunktion heißt *category utility*. \(\text{CU} = \sum_{k=1}^K \sum_{i=1}^I \sum_{j=1}^{J(i)} P(A_i = V_{ij}) P(A_i = V_ij | C_k) P(C_k | A_i = V_{ij}).\)
-   Für jedes x wird berechnet, durch welche Aktion CU am größten wird (Erzeuge Knoten, Vereinige 2 Knoten, Teile Knoten auf)

Bayessches Lernen
=================

Allgemeines
-----------

\[P(B|A) = \frac{P(A|B)P(B)}{P(A)}.\]

Wir suchen die *maximum a posteriori* (MAP) Hypothese \(h_\text{MAP}\): \[\begin{aligned}
h_\text{MAP} &= \argmax\limits_{h \in H} P(h|\text{Daten}) \\
&= \argmax\limits_{h \in H} \frac{P(\text{Daten}|h)P(h)}{P(h)} \\
&= \argmax\limits_{h \in H} P(\text{Daten}|h)P(h).
\end{aligned}\]

Die Annahme \(P(h_i) = P(h_j)\) führt zur *Maximum-Likelihood-Hypothese*. Das bedeutet wir haben kein Vorwissen über die priors. \[h_{\text{ML}}= \argmax\limits_{h \in H} P(\text{Daten}|h).\]

Naiver Bayes Klassifikator
--------------------------

![Eine geklaute Folie sagt mehr als 1000 Worte](media/bayes-naiv.png)

Bayessche Netze
---------------

Bedingte Unabhängigkeit des Naiven Bayes zu restriktiv ⇒ Beschreiben bedingter (Un)Abhängigkeiten

Sonstiges
---------

aka Sachen, die in den Prüfungsprotokollen nie gefragt werden.

-   Konzeptlernen
-   Lernen einer rell-wertigen Funktion
-   Optimaler Bayes-Klassifikator
-   Expectation-Maximization Algorithmus (siehe auch HMM)

Entscheidungsbäume
==================

ID3
---

``` python
while !Trainingsdaten perfekt abgebildet:
    k = nächster Knoten
    A = bestes Entscheidungsattribut für k
    k.attribut = A

    for v in (alle Attribute, die A annehmen kann):
        b = k.erstelle_neues_blatt(v)
        b.daten = (|positive Beispiele von v|, |negative Beispiele von v|)
```

Das beste Attribut ist das, durch das die Entropie minimiert wird. \[\text{Entropie}(S) = -p_\oplus \log_2p_\oplus - p_\ominus \log_2 n_\ominus\] \(S\) ist hier die Trainingsmenge, \(p_\oplus\) der Anteil der positiven Beispiele ist und \(p_\ominus\) der Anteil der negativen Beispiele.

Der *Gewinn* den man durch A erhält ist die erwartete Reduzierung der Entropie durch die Einsortierung der Attribute über A. \[\text{Gewinn}(S, A) \equiv \text{Entropie}(S) - \sum_{v \in V(A)}
\frac{|S_v|}{|S|}\text{Entropie}(S_v).\] Durch diese Minimierung der Entropie erreicht man einen Baum mit niedrigerer Tiefe.

![Beispiel zur Gewinnberechnung](media/trees-entropy.png)

![Beispiel zur Gewinnberechnung bei vollständiger Unterteilung](media/trees-entropy2.png)

### Verschiedene Attribut-Erweiterung

#### Attribute mit vielen Werten

\(\text{Gewinn}(S, A)\) bevorzugt Attribute mit vielen Werten.

Lösung: Bestrafen von Attributen durch Normierung mit *Splitt Information*.

#### Kontinuierliche Attributwerte

Wähle als Schwellwert das Mittel der Klassenmittel.

#### Fehlende Attributwerte

Setze bei fehlenden Werten

-   den häufigsten Wert der Beispiele
-   den häufigsten Wert der Beispiele der gleichen Klasse
-   einen zufälligen Wert mit der Verteilung der anderen Werte

### Reduced Error Pruning

Um Overfitting zu vermeiden, werden sukzessive die Knoten entfernt, deren Entfernung die Klassifikation der Testdaten am meisten erhöht.

### Induktiver Bias

ID3 bevorzugt

-   Attribute mit großem Informationsgewinn nahe der Wurzel
-   kleine Bäume

C4.5
----

Weiterentwicklung von ID3 mit regelbasiertem Pruning

Beispiel:

``` fortran
IF (Vorhersage = sonnig) AND (Luftfeuchtigkeit = hoch)
    THEN (Tennis = nein)
IF (Vorhersage = sonnig) AND (Luftfeuchtigkeit = normal)
    THEN (Tennis = ja)
```

### Rule Post-Pruning

``` python
Trainiere Baum
Konvertiere Baum in IF-THEN-Regeln

while !Genauigkeit wird schlechter:
    Generalisiere/"Prune" Regeln (d.h. entferne Vorbedingungen)

Sortiere Regeln nach Klassifikationsgüte und verwende Sie in dieser Reihenfolge
```

ID5R
----

Ist wie ID3 aber inkrementell. Knoten speichern Zähler für jeden möglichen Attributwert. Algorithmen für Update und Beispiel: siehe Skript weil mäh.

Random Forests
--------------

Mehrere zufällige Entscheidungsbäume mit majority voting.

-   Kein Pruning
-   Benutze zufällige Untermenge der Attribute/ der Trainingsdaten
-   Bäume sollen möglichst unkorreliert sein
-   Bootstrap für Trainingsdaten

Hidden Markov Modelle
=====================

Ein HMM ist ein Fünf-Tupel \(\lambda = \{S,V,A,B,\Pi \}\).

\(S\)  
Menge der Zustände \(\{S_1, \dots, S_N\}\)

\(V\)  
Menge der Ausgabezeichen \(\{v_1, \dots, v_M\}\)

\(A\)  
Matrix der Übergangswahrscheinlichkeiten \((a_{ij})\) um von \(S_i\) nach \(S_j\) zu kommen

\(B\)  
Menge der Emissionswahrscheinlichkeiten \(b_i(k)\) in \(S_i\) das Zeichen \(v_k\) auszugeben

\(\Pi\)  
Menge der Anfangswahrscheinlichkeiten \(\{\pi_1, \dots, \pi_N\}\), so dass \(S_i\) der Startzustand ist.

Arten von HMM
-------------

Ergodisch  
Jeder Zustand ist von jedem anderen erreichbar

Links-nach-rechts-Modell (Bakis)  
der Zustandsindex steigt monoton mit der Zeit

Evaluationsproblem
------------------

= Was ist die Wahrscheinlichkeit, die Ausgabesequenz \(O\) rauszubekommen? Das heißt was ist \(P(O|\lambda)\)?

Lösung: *Forward-Algorithmus*. Hier werden die Wahrscheinlichkeiten für alle Zustände im Zeitschritt \(t\) aus den Zuständen zur Zeit \(t-1\) berechnet: \[\begin{aligned}
\alpha_1(i) &= \pi_i b_i(O_1), \\
\alpha_{t+1}(j) &= b_j (O_{t+1}) \sum_{i=1}^N \alpha_t (i) a_{ij}, \\
P(O|\lambda) &= \sum_{i=1}^N \alpha_T(i).
\end{aligned}\]

Dynamisches Programmieren wird verwendet um bereits ausgerechnete \(\alpha\) wiederzuverwenden.

Der *Backward-Algorithmus* funktioniert analog zum Forward-Algorithmus, fängt aber “von hinten” an. \[\begin{aligned}
\beta_T(i) &= 1, \\
\beta_t(i) &= \sum_{i=1}^N a_{ij} b_j (O_{t+1}) \beta_{t+1}(j).
\end{aligned}\]

Dekodierungsproblem
-------------------

= Was ist die Zustandsfolge \(s_1 s_2 \dots s_N\), die \(O\) am wahrscheinlichsten hervorgebracht hat?

Lösung: *Viterbi-Algorithmus*. [Eine Erklärung](https://martin-thoma.com/apply-viterbi-algorithm/).

Lernproblem
-----------

= Wie muss ich die Parameter \(\lambda\) anpassen, so dass \(O\) wahrscheinlicher wird? Das heißt wie findet man \(\hat\lambda = \argmax\limits_\lambda P(O|\lambda)\)?

Lösung: *Baum-Welch-Algorithmus*

Markov Logik Netze
==================

= Prädikatenlogische Formeln + Verbindungen

Die Formeln sind gewichtet und erlauben Widersprüche (⇒ *soft constraint*)

Lernen
------

Suche der Gewichte, die am wahrscheinlichsten die Daten generiert haben. Irgendwie spielt da diskriminatives Lernen ne Rolle.

Aaach das kommt doch eh alles nicht dran

Instanzbasiertes Lernen
=======================

Instanzbasiertes Lernen ist *lazy learning*. Beim Lernprozess werden die Trainingsbeispiele nur abgespeichert. Deswegen ist das Lernen auch sehr schnell. Bei der Klassifikation hingegen muss jedes Mal eine Approximation der Zielfunktion erstellt werden.

k-Nearest Neighbor (k-NN)
-------------------------

Hat den induktiven Bias, dass Instanzen so wie ähnliche Instanzen klassifiziert werden (wer hätts gedacht).

### Aufbau

-   Distanzfunktion
-   Anzahl der Nachbarn
-   Gewichtung nach Distanz

### Pro/Kontra

Pro:

-   Robust gegen verrauschte Trainingsdaten (insbesondere mit Gewichtung)
-   Training schnell

Kontra:

-   Distanzmaß gewichtet alle Attribute gleich stark ⇒ Curse of Dimensionality
-   Speicherorganisation
-   Klassifikation langsam

Case Based Reasoning (CBR)
--------------------------

Allgemeines Framework des “analogen Schließens”. Neue Fälle werden mit ähnlichen, schon gelösten Fällen verglichen und eine Lösung, die schonmal funktioniert hat, wird dem neuen Fall angepasst.

Ein Fall ist eine aufgetretene Problemsituation + resultierende Erfahrungen und besteht aus mindestens:

-   Problembeschreibung
-   Lösung(sversuch)
-   Ergebnis

### Der CBR-Zyklus

![CBR-Zyklus](media/cbr.png)

#### Retrieve

Ähnliche Fälle werden gefunden anhand

-   ähnlicher Syntax (*knowledge-poor*)
-   ähnlicher Semantik (*knowledge-intensive*)

Effektives Retrieval durch *Indexstrukturen*. Indizes sind z.B. Features mit hoher Aussagekraft.

#### Reuse

Lösung muss adaptiert werden:

-   Keine Adaption, nur Übertragen
-   durch Benutzer adaptiert
-   automatische Adaption (*transformational* oder *derivational*)

#### Revise

Die Lösung wird evaluiert und verbessert.

#### Retain

-   Neuen Fall speichern
-   Alten Fall generalisieren
-   Neue Merkmale/Indizes

### Pro/Kontra

Pro:

-   Kann mit wenig Information arbeiten
-   Lernen ist einfach (one-shot)
-   Günstig für Probleme die durch Regeln schlecht erfassbar sind
-   …

Kontra:

-   Speicherkosten
-   Klassifikation kann lange dauern
-   Hängt stark von Repräsentation ab
-   …

Deduktives Lernen
=================

Deduktive Lernverfahren benutzen Hintergrundwissen über die Problemdomäne und brauchen wenig/keine Lernbeispiele.

Beispiel Schach-Gabel:

-   Induktiv: viele Beispiele nötig
-   Deduktiv: Bereichstheorie (hier: Schachregeln) erlaubt, wesentliche Merkmale zu extrahieren

Erklärungsbasiertes Lernen (EBL)
--------------------------------

-   Prozess, der implizites Wissen in Explizites umwandelt
-   System muss erklären können, wieso ein Trainingsbeispiel eine Ausprägung des zu lernenden Konzepts darstellt
    -   Dazu brauchst es erklärende Fähigkeiten

### Bestandteile eines EBL-Systems:

Zielkonzept  
Beschreibung des zu lernenden Konzepts

Trainingsbeispiel  
Beispiel für das Konzept

Bereichstheorie (domain theory)  
Regeln und Fakten die erklären wieso das Trainingsbeispiel ein Beispiel für das Zielkonzept ist

Operationalitätskriterium  
Prädikat über Konzeptbeschreibung, das Form der erlernten Beschreibungen spezifiziert

Gesucht:

-   **Generalisierung** des Trainingsbeispiels, das
    -   hinreichende Definition des Zielkonzepts darstellt
    -   Operationalitätskriterium erfüllt

### Erklärungsbasierte Generalisierung

Ziel: Trainingsbeispiel ⇒ Generalisierung

#### Schritt 1: Explain

-   Konstruiere in der Bereichstheorie eine Erklärung, wie das Trainingsbeispiel das Zielkonzept erfüllt.
-   Konstruiere die Erklärungsstruktur als Baum so, dass die Blätter das Operationalitätskriterium erfüllen

![Beweisbaum für das Zielkonzept `robust(x)`](media/dl-ebl.png)

Die Trainingsbeispiele im Baum oben sind `age(Num5, 5)`, `robot(Num5)`, `r2d2(Num5)` und `manufacturer(Num5, GR)`.

#### Schritt 2: Generalize

-   Bestimme *hinreichende Bedingungen*, so dass die in Schritt 1 gefundene Erklärungsstruktur gültig ist
-   Formuliere diese Kriterien in Terme, die das Operationalitätskriterium erfüllen

![Generalisierung des Beweisbaums](media/dl-ebl2.png)

### Notwendigkeit von Beispielen

-   EBL kann ich manchen Bereichen keine Beispiele selber erzeugen
-   Beispiele sind Fingerzeige für Deduktion
-   Man kann Beispiele als *typisch* auswählen

### Wird tatsächlich gelernt?

EGB verändert deduktive Hülle der Wissensbasis *nicht*. Das heißt es wird nichts Neues gelernt. Aber in der Praxis sind Zeit/Rechenleistung begrenzt ⇒ Lernen von Makros/Generalisierungen - *Speedup-Learning*.

Den Aufwand für das Lernen neuer Regeln kann man reduzieren:

-   Aufnahme neuer Regeln/Makros beschränken
-   Nützlichkeit neuer Regeln messen
    -   Fürs Messen ggf. Beispielprobleme lösen
-   Regel je nach Ergebnis erhalten oder verwerfen

### Pro/Kontra

Pro:

-   Gelerntes Wissen ist korrekt
    -   ! nur falls ursprüngliches Wissen korrekt
-   kein impliziter induktiver Bias
-   explizite Formulierung der Bereichstheorie

Kontra:

-   wird mysteriöserweise verschwiegen ;)

Hybride Lernverfahren
---------------------

Kann man induktives und deduktives Lernen kombinieren?

<table style="width:71%;">
<colgroup>
<col width="16%" />
<col width="27%" />
<col width="26%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"></th>
<th align="left">Induktives Lernen</th>
<th align="left">Deduktives Lernen</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Ziel</td>
<td align="left">Hypothese passt zu Daten</td>
<td align="left">Hypothese passt zu Bereichstheorie</td>
</tr>
<tr class="even">
<td align="left">Rechtfertigung</td>
<td align="left">Statistische Inferenz</td>
<td align="left">Deduktive Inferenz</td>
</tr>
<tr class="odd">
<td align="left">Vorteile</td>
<td align="left">benötigt wenig Vorwissen</td>
<td align="left">benötigt wenige Beispiele</td>
</tr>
<tr class="even">
<td align="left">Probleme</td>
<td align="left">Spärliche Daten, inkorrekt gewählter Bias</td>
<td align="left">imperfekte Bereichstheorie</td>
</tr>
</tbody>
</table>

![Vergleich induktives vs. deduktives Lernen](media/dl-hybrid.png)

Versuch: Finde Hypothese die sowohl zu Trainingsbeispielen als auch Bereichstheorie passt, z.B. mittels \[\argmin\limits_{h \in H} k_I E_I(h) + k_D E_D(h)\] wobei \(E_I, E_D\) die induktiven bzw. deduktiven Fehlerraten sind und \(k_I, k_B\) die induktiven bzw. deduktiven relativen Gewichte.

### Knowledge-Based Artifical Neural Networks (KBANN)

-   Initialisere NN mittels Bereichstheorie
-   Verfeinere Netz durch Backpropagation und Trainingsbeispiele

#### Trainingsalgorithmus

``` python
Pro Instanzattribut ein Eingabeneuron erstellen

for Klausel in Bereichstheorie:
    Neues Neuron erstellen
    Eingänge mit geprüften Attributen verknüpfen und Schwellwert setzen

Verbinde Schichten mit jeweils nächster Schicht (kleine zufällige Gewichte)

Backpropagation mit Trainingsbeispielen anwenden
```

![Beispiel für ein generiertes KBANN aus Klauseln](media/dl-kbann.png)

#### Anwendungen

-   Lernen von physikalischen Objektklassen (Beispiel: Zielkonzept *Tasse*)
-   Erkennen biologischer Konzepte in DNA (Beispiel: bestimmte genetische Regionen - *Promotoren*)
    -   KBANNs zeigen gute Resultate

Evolutionäre Algorithmen
========================

Grundalgorithmus
----------------

``` python
while !optimal:
    Selektiere Eltern
    Generiere Nachkommen
    Bewerte Fitness von Nachkommen
    Selektiere überlebende Populationsmitglieder nach Fitness
```

Generierung von Nachkommen
--------------------------

Exploration vs. Exploitation

Exploration  
Erforschen des Hypothesenraums

Exploitation  
Lokale Optimierung

-   Exploration findet wahrscheinlicher schlechtere Nachkommen
-   Exploitation birgt die Gefahr der lokalen Minima
-   ⇒ Explorationsgrad gemäß Fitness der aktuellen Generation wählen

### Mutation

⇒ Nachkomme stammt von einem Elternteil ab

-   Einen Teil der Bits zufällig ändern
-   Eine Teilsequenz invertierena oder n anderer Stelle einfügen

### Rekombination

⇒ Nachkomme stammt von mehreren Elternteilen ab

![](media/ea-recombination.png)

![](media/ea-recombination2.png)

Selektion
---------

Zwei Arten der Selektion:

-   Selektion der Eltern für die Erzeugung von Nachkommen (*Mating*)
-   Selektion der Population der nächsten Generation (*Iteration*)

### Populationsmodelle (⇒ Mating)

Inselmodell (lokal)  
Evolution läuft getrennt, nur manchmal werden Individuen ausgetauscht

Nachbarschaftsmodell (nahe Umgebung)  
Nachkommen werden nur von Individuen erzeugt, die die beste Fitness in ihrer Nachbarschaft besitzen

Eine einfache Menge (global)  
Die global Besten entwickeln sich rasch weiter andere Entwicklungslinien werden unterdrückt

### Populationsmitglieder (⇒ Iteration)

Wird von 3 Parametern bestimmt

\(\mu\)  
Populationsgröße

\(\lambda\)  
Anzahl der Nachkommen

\(\rho\)  
Anzahl der Eltern

-   \((\mu, \lambda)\)-Strategie: Auswahl bezieht sich nur auf Nachkommen (⇒ Exploration)
-   \((\mu + \lambda)\)-Strategie: Auswahl bezieht Eltern ein, Suche nach Eliten (⇒ Exploitation, günstig bei gut berechenbarer Fitnessfunktion)

#### Selektionsmethoden

Verschiedene Formen der Ersetzung:

-   Nachkommen ersetzen alle Eltern (Generationenmodus)
-   Nachkommen ersetzen Teil der Eltern
-   Nachkommen ersetzen ähnlichste Eltern
-   Bestes Individuum überlebt (Elitemodus)

Daumenregel (Braun):  
Beste 1/4 der Population sollen 3/4 der Nachkommen erzeugen.

##### Fitness-based Selection

Wahrscheinlichkeit \(P(x)\) das \(x\) ausgewählt wird ist abhängig von der relativen Fitness \(f(x)\). Normiert durch Anteil der Nachkommen an der Population. \[P(x) = \frac{\lambda}{\mu} \frac{f(x)}{\sum_{x' \in \text{Pop.}} f(x')}\]

##### Ranking-based Selection

Wahrscheinlichkeit hängt ab vom Fitnessrang \(r(x)\) und dessen Güte \(g(x) = a^{-x}\) (das ist nurn Beispiel für ne Gütefunktion). ⇒ weniger abhängig von der Fitnessfunktion. \[P(x) = \frac{g(r(x))}{\sum_{x' \in \text{Pop.}} g(r(x'))}\]

##### Tournament Selection

Wähle aus Gruppen von n Individuen jeweils das beste aus.

### Probleme

Genetischer Drift  
Individuen vermehren sich zufällig mehr als andere

Crowding, Ausreißerproblem  
fitte Individuen und ähnliche Nachkommen dominieren Population

-   Entwicklung der Individuen verlangsamt
-   Vielfalt der Population eingeschränkt

Lösung:

-   Verschiedene Populationsmodelle, Selektionsmethoden
-   Populationsgröße optimieren

Arten von Evolution
-------------------

Lamarksche Evolution  
-   Individuen ändern sich/lernen nach Erzeugunug
-   Genotyp ändert sich

Baldwinsche Evolution  
-   Individuen ändern sich auch hier nach Erzeugnug
-   Genotyp bleibt unverändert

Hybride Verfahren  
Haben veränderbare oder fixe Phänotypen

Genetische Programmierung
-------------------------

Hintergrund: Kinematisches System, Roboter der sich in Simulation bewegt. Die Fitnessfunktion ist die Abweichung von einer vorgegebener Trajektorie. Individuen sind Programme gespeichert in Baumstruktur.

Mikromutation  
Veränderung einzelner Werte einer Instruktion

Makromutation  
Austausche ganzer Instruktionsblöcke

Mikrocrossover  
Änderung eines Parameters einer Instruktion

Makrocrossover  
Austausch einer Instruktion

Homologes Crossover  
Austausch von Instruktionen eines spezifischen Gelenks

Pro/Kontra
----------

Pro:

-   Gut parallelisierbar
-   Optimierungsprobleme in Simulation lösbar

Kontra:

-   Meist sehr rechenintensiv
-   Um anwendungsspezifisches Wissen einsetzen zu können, müssen allgemeine Algorithmen spezialisiert werden

Klausur
=======

Aufgaben: 1. Für verschiedene Probleme entscheiden, ob man ML einsetzen sollte 2. Reinforcement Learning erklären - Q-Algorithmus vervollständigen - Was ist ein Markov Decision Process - Naive Bayes Beispiel rechnen - Biases erklären - Probleme beim Lernen erklären (Komplexitätsproblem) - Definition Entscheidungsbaum - Wie funktioniert ID3 - Was ist Entropie, Gewinn - Q vs Eligibility-Traces Pfad auf Karte zeichnen - Verfahren beschreiben, dass SRM nutzt und wieso das was bringt
