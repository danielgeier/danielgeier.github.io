---
layout: post
title:  "Grundlagen der automatischen Spracherkennung  (WS15/16, Dr. Stüker)"
date:   2016-03-19 00:00:00 +0100
categories: posts
---

Einführung
==========

Spracherkennung
:   Automatische Umwandlung menschlicher, gesprochener Sprache in die dazugehörige Wortsequenz in maschinenverarbeitbarer Form.

    - != Voice Recognition (= Sprechererkennung)
    - != Natural Language Understanding (= Sprachverstehen)
    - Wortsequenz != Satz
        - Keine Struktur (z.B. keine Zeichensetzung, oft grammatikalisch inkorrekt)

Anwendungen
-----------

- Diktat (wenn man diktieren kann)
- Hands-free Bedienung von Geräten
	- *pick-to-Voice* Systeme (Waren für Bestellungen aus Lager zusammensuchen)
- Natürlichsprachliche Interaktion (Roboter, Siri)
- Sprachübersetzung (Google Translate, Lecture Translator)
- Bildung (Fremdsprachlernprogramm)
- Barrierefreiheit
	- Untertitlung (Audio im TV zu schlecht -> Filter der Audio verbessert (= Mensch der nachspricht :D) -> ASR)
		- Für schwere Wörter (z.B. Eigennamen) Makros erstellen
- Indexierung von Audio-/Videodateien
- Intelligente Räume (mit \~\~Informationsfusion\~\~)
	- Protokollerstellung für Meetings

Vorteile
--------

- Praktisch
    - Hände und Augen sind frei (z.B. für Ärzte bei OP: "Zeig mal die Aorta")
    - Natürliche Eingabe
    - Tragbar
    - Höhere Bandbreite als Schrift
- Geeignet als zusätzliche Modalität

Nachteile
----------

-   Braucht guten Sound -- Probleme:
    -   Umgebungen, in den Stille erforderlich ist
        - => Flüstererkennung
    -   Sehr laute Umgebungen (Cockpit)
    -   Störgeräusche (Auto)
    -   Mikro muss nah platziert sein
        -   => Signal von mehreren Mikros kombinieren
-   Erkennt (noch) nicht so gut wie ein Mensch
    - Mit Fehlern muss man in der Anwendung rechnen und umgehen

## Taxonomie von Sprache
- Einzelne Kommandos
- Künstliche Sprachen
- Natürliche Sprachen
    - Gelesen < geplant < ungeplant

## Taxonomie von ASR Systemen/Aufgaben

- Sprecherabhängigkeit ((un)abhängig, evtl. geschlossene Sprechermenge)

- Größe des Diskurs (domänenlimitiert, domänenunabhängig)

- Sprecherverhalten ((un)kooperativ, vertraut)

- Äußerungsart (Wörter < Phrasen < kontinuierliche Sprache)

Schwierigkeiten
---------------

### Variabilität

-   Auf Signalebene (Stimme, Umgebung, Mikro, unsegmentiert)
-   Auf phonetischer Ebene (Dialekte, eSprechgeschwindigkeit, *Lombard-Effekt*)
-   Auf linguistischer Ebene (Homophone (Verse vs. Ferse), Homonyme (Tau = Seil oder τ))

    > Time flies like an arrow

### Menschen

- Variabilität: kann man wegabstrahieren
- Segmentation: macht Gehirn 1a
- sehr großes Hintergrundwissen (Semantisch, syntaktisch, akustisch)
- multimodal Klassifikation (Gesten, Mimik => *McGurk-Effekt*)





Sprachproduktion
----------------
Lunge --Luftstrom--> Luftröhre -> Stimmlippen (Bernoulli-Effekt) -> Vokaltrakt

Grundfrequenz bei Männern ~125 Hz, Frauen ~250Hz

![IPA Vokaltrapez beschreibt Vokale abhängig von Kiefer- und Zungenposition](media/ipa-vowel-chart.svg)

Schriftsysteme: Logosyllabary, Syllabary, Abjad, Alphabet, Abugida, Featural

Schall
------

Kritische Bandbreite
:   Kleinste Frequenzdifferenz ab welcher Töne unterschieden werden können. Variert über das Spektrum.

Menschen hören am besten zwischen 2 und 5 kHz.

Mikrofone: Dynamisch, Kondensator, Elektret, Kohle, Piezo, Grenzflächenm., Richtrohrm., ...

Richtcharakteristiken: Kugel, Acht, Keule, ...


## Word Error Rate

Minimale Editierdistanz von erkannten Wörtern zu Trainingsbeispiel

  $$\text{WER} = \frac{\text{#insertions} + \text{#deletions} + \text{#substitutions}}{\text{#Wörter in Referenz}}.$$

  Nachteile:

  * Kosten unabhängig von Schwere des Fehlers (z.B. ein Buchstabe anders vs. komplett anderes Wort, "nicht" vergessen) (=> Zeichenfehlerrate (bedingt))
  * Manche Schriften segmentieren nicht nach Wörtern (z.B. Chinesisch; => Zeichenfehlerrate)

Word Accuracy Rate
: $$\text{WA} = 1 - \text{WER}.$$

Vorverarbeitung
===============
Relevante Information extrahieren, Redundanzen reduzieren, Irrelevantes (Sprecheridentität, Lautstärke, ...) wegfiltern

Ein System $$T$$ wandelt eine Eingabe $$x[n]$$ in ein Ausgabe $$y[n]$$ um --- $$T\{x[n]\} = y[n]$$

* *linear*, falls

$$T\{a_1x_1[n]+a_2x_2[n]\} = a_1T\{x_1[n]) + a_2 T\{x_2[n]\}.$$

* *zeitinvariant*, falls aus $$T\{x[n]\} = y[n]$$ folgt, dass

  $$T\{x[n - n_d]\} = y[n - n_d]$$

  wobei $$n_d$$ eine Verzögerung ist.

Fouriertransformation
---------------------

### Dirac-Distribution
Annahme: $$\int_{-\infty}^\infty a\, \delta (x)\, dx = a$$
=> beliebig schmale, beliebig hohe Funktion mit Fläche 1.

* Siebeigenschaft:

  $$\int_{-\infty}^\infty \delta(x - \tau)\, g(x)\, dx = g(\tau).$$

### Faltung

$$(f * g)(t) \stackrel{\mathrm{def}}{=} \int_{-\infty}^\infty f(\tau)\, g(t - \tau)\, d\tau$$

### Impulsantwort

bei LTI einfach

$$y[n] = \sum_{k = - \infty}^\infty x[k]\, h[n-k] = x[n] * h[n].$$

z.B. Sprache aus Mund ist (Luftstrom aus Lungen + Rauschen) * (Impulsantwort Vokaltrakt) * (Impulsantwort Lippen/Nase)


### Kontinuierliche Fouriertransformation

$$F(\omega) = \int_{-\infty}^\infty f(t)\, e^{-i \omega t} \, dt$$

$$f(t) = \frac{1}{2\pi}\int_{-\infty}^\infty F(\omega)\, e^{i \omega t} \, d\omega$$

- Erweiterung der Fourrierreihe auf nicht periodische Funktionen
- Vorstellung: Periode geht gegen unendlich

Spektrum ($$F(\omega)$$)
: Gibt Amplitude und Phase des Signals an der Stelle wieder

Amplitudenspektrum ($$\vert F(\omega)\vert$$)
: nur noch Amplitude

Phasenspektrum ($$\phi(\omega)$$)
: nur noch Phase

Leistungsspektrum ($$\vert F(\omega)\vert^2$$)
: quadrierte Amplitude (Begriff "Leistung" aus der Physik)

Menschliche Sprache ist *mittelwertfrei*, d.h. gleichmäßig um 0 gestreut. Der mittelwertfreie Anteil der Fouriertransformierten ist nicht Sprache sondern der Kanal. Und den kann man dann einfach subtrahieren.

Komplexes Spektrum:

- Darstellung im Einheitskreis mit Achsen Im und Re
- Winkel des Pfeils ist Phasenverschiebung
- Länge des Pfeils ist Amplitude
- Frequenz ist 3. Dimension

### Zeitdiskrete Fouriertransformation
Für diskrete Signale
Aber: Resultat ist wieder kontinuierlich

### Diskrete Fouriertransformation (DFT)
Funktioniert nur für periodische Signale

### Schnelle Fouriertransformation (FFT)
"FFT ist keine Transformation, sondern ein Algorithmus der die DFT berechnet"
berechnet DFT mit divide-and-conquer in $$O(n\log n)$$

### Zusammenfassung

| | Signal darf nicht periodisch sein | Output
|kontinuierliche FT | ✓ | komplexes, kontinuierliches Spektrum
|zeitdiskrete FT    | ✓ | komplexes, kontinuierliches Spektrum
|diskrete FT        | ✗ | Phase & Amplitude der Frequenzbänder in komplexe Zahlen

Digitalisierung
----------------

### wertdiskret (Quantisierung)
- Werteraum in n Intervalle aufteilen
- Quantisierungsrauschen: max - min / 2n

### zeitdiskret (Abtastung)
- mathematisch mit Dirac-Stößen => *Dirac-Kamm*
- in echt mit Halteglied (*sample-and-hold* Schaltung)
- Nyquist-Shannon-Theorem
 : Sample-Rate muss doppelt so groß sein wie höchste Frequenz, um Signal exakt zu rekonstruieren (sonst Aliasing).

# Protokollfragen

## Tipps
- kommt immer dran:
    - Mikrofone
    - Vorverarbeitung
    - HMM, zb
        - Def HMM
        - 3 Probleme + Loesungen
    - Fundamentalformel
- Literatur benutzen
- general & life advice
    - praezise antworten
    - denken, dann reden (schlechter: erst falsch und dann korrigieren)
    - Wenn man absolut keinen Plan hat, dann sagen sonst wird 10min hergeleitet
    - 1.0 guy sagt:
        - Zuhoeren und Pruefer ausreden lassen
        - denken
        - Satz formulieren, der genau die Frage beantwortet
        - antworten

## Intro
- Def ASR
- Ist Spracherkennung eher einfach oder schwierig? und wieso?

- Mensch
    - Wie funktioniert der menschliche Sprachapparat?
    - Wie wird Sprache erzeugt?
    - Schwingt die Stimmritze immer?
    - Auch Gehoerschnecke und so... ALLL OF IIIITT
    - Vokale Konsonanten Unterschied
    - stimmhafte Konsonanten?
    - Vokale durch was moduliert
    - Konsonanten durch was moduliert
    - Warum vibrieren Stimmbaender?
    - Welche lautlichen Einheiten gibt es

- Mikrofon
    - Unterteilung und Unterschiede
    - Wann was verwenden
    - Abtastfrequenz
    - Spielt beim Kondensatorm. Druck oder Energie des Schalls eine Rolle?
    - Digitalisierung (Diskretisierung & Quantisierung)
        - Welche Frequenzen wann wo? (wat)
        - Wie viele Quantisierungsstufen?
            - Welchen Effekt? (Rauschen)
        - Wo liegt menschliche Hoerschwelle?
        - Welchen Schalldruckpegel hat menschliche Sprache normalerweise?
        -  u-law
        - Wo besser aufloesen? (oberer (lauter) Bereich)


## Vorverarbeitung
- Was sind die einzelnen Schritte einer typischen Vorverarbeitung?
- Alles superdupi erklaeren
- DFT
    - Wieso Betrag nehmen?
- Fensterung
    - Wieso 10ms Verschiebung?
    - Arten von Fenstern
    - Wie viele Vektoren hat man in einem Fenster?
    - Wieso nicht Rechtecksfenster? Formel dazu (vgl Faltung = sinc)
    - Wieso 16ms Groesse gut?
        - bei 16khz sind das 256 Samples => gut fuer FFT
- Warum Mel-Skalierung?
- Warum Logarithmierung?
- Warummmm
- Wieso Phase unwichtig? (Daten sparen & menschl. Wahrnehmung)
- Cepstrum
    - Hinmalen und beschreiben
    - Welche Frequenzen wegschmeißen?

## Spracherkennung
- Moegliche Ansaetze erklaeren (det., stoch.) + Schaubild
- Fundamentalformel mit Bestandteilen erklaeren
    - Was kann man weglassen und wieso?

## Akustisches Modell
- Was wird verwendet?
- HMM Definition
- 3 Probleme + Loesungen (Formeln + Algorithmen koennen)
    - gamma bei Viterbi erklaeren
    - Was ist Training? Was ware wuenschenswert? (Max Lik)
    - Wie Parameter bei EM-Algorithmus fuer gauss mix. initialisieren?
- Wieso EM nicht gut?
- Was fuer kont. HMMs

## Sprachmodell
- Was nehmen?
- Wie P(w1w2...wN) schaetzen?
    - Warum nicht direkt?
- Probleme (+ Loesungen)?
- Was ist
    - Backoff
    - Discounting
    - Interpolation

## Suche
- zeitsynchron vs asynchron?
- A*
- Welche Heuristiken fuer Spracherkennung?
- Strahlsuche? Viterbi Strahlsuche?
- Warum suchen wir nach der besten Zustandsfolge?
