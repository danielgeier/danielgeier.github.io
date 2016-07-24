---
layout: post
title:  "Sprachtechnologie und Compiler"
date:   2016-04-26 00:00:00 +0100
categories: posts zusammenfassung
multipage: true
---

![Darum geht's](../../media/ueberblick.png)

Semester: SS 2016

Dozent: Prof. Snelting

$$\DeclareMathOperator{\syn}{syn}
\DeclareMathOperator{\inh}{inh}
\DeclareMathOperator{\AF}{AF}
\DeclareMathOperator{\adr}{adr}
\DeclareMathOperator{\llbracket}{[\![}
\DeclareMathOperator{\rrbracket}{]\!]}$$


### Wozu Sprachtechnologie?

- Compiler
- Entwicklungswerkzeuge (zB Semantische Analyse in der IDE)
- DSLs
- Beschreibung von Daten (HTML, XML)
- Hardware-Entwicklung (VHDL)

#### Zielkriterien für Compiler

- Compilation
    - korrekt
    - schnell
- erzeugtes Programm
    - schnell
    - klein
    - energiesparend
- UX
    - hilfreiche Fehlermeldungen

### Unterarten

- Reiner Interpreter (Bash)
- Zwischencode-Interpreter (Java, Python)
- JIT-Compiler (JVM)
- Vollständige Übersetzung (C, C++)


Lexikalische Analyse
====================

Bezeichnung: *Lexer*, *Scanner* oder *Tokenizer*

- [ Lexer von `clang`](https://github.com/llvm-mirror/clang/blob/4a65931dcba82b23856d654eb77d133d0a3c59f2/lib/Lex/Lexer.cpp#L2931) (+ [doxygen docs](http://clang.llvm.org/doxygen/Lexer_8h.html) dazu)


## Endlicher Automat

$$A = (S, \Sigma, \delta, s_0, F)$$

- Zustände $$S \neq \emptyset$$
- Eingabealphabet $$\Sigma$$
- Übergangsrelation $$\delta \subseteq S \times (\Sigma \cup \{\epsilon\}) \times S$$
- Anfangszustand $$s_0 \in s$$
- Endzustände $$F \subseteq S$$
\
- A ist *deterministisch* $$\iff$$ keine mehrdeutigen oder $$\epsilon$$-Übergänge

- Prinzip des längsten Musters
	- Automat liest, bis neues Zeichen nicht mehr zum Token gehört

- Fehlerzustände nach allen Umformungen einfügen
	- Für jedes von $$s$$ unbehandelte Zeichen Übergang zu $$s_\text{err}$$ einfügen

### Algorithmen
- Umwandlung Regex -> endlicher Automat
- Elimination von $$\epsilon$$-Übergänge
- NFA in DFA umzuwandeln (Teilmengenkonstruktion, muss man nicht können)
- Minimalautomat (Kongruenzklassen, muss man nicht können)

### Tokenidentifikation

Wie unterscheiden zwischen Schlüsselwort (`int`) und Bezeichner (`myint`)?

- Für jedes Schlüsselwort eigenen Endzustand: nervig (tausende zusätzliche Zustände), oder...

- Schlüsselwörter als Bezeichner erkennen, später mit Stringtabelle unterscheiden
    - Let's do this

## Implementierungstechniken

### Als Datenstruktur

- Einfacher, übersichtlicher
- Code für Aktionen umständlich

```c
int table[|S|][|Σ|];
char c;
int result = ERROR;
int state = 0;

while (true) {
    c = next_char();

	if (is_final(state))
		result = state;

    state = table[state][c];

	if (is_error(state))
		break;

}
// Gibt letzten Endzustand vor Fehler aus
// Fehlt: evtl. Eingabe zurücksetzen
return result;
```

#### Als Programm

- Zustand implizit in Programmposition
- Weniger Befehle pro Zeichen [wirklich?]
- Code für Aktionen leicht einbaubar

```c
int scan_id() {
	do {
		c = next_char();
	} while(ischar(c)||isdigit(c));
	return ID;
}

int scan() {
	switch (c) {
	case '(':
		c = next_char();
		return LP;
	case ')':
		c = next_char();
		return RP;
	case 'a'-'z':
		return scan_id();
	}
	return ERROR;
}
```

### Handgeschrieben vs. Generator
- Generator
	- kompakt, erprobt, performant, schnelles Prototyping
	- Integration umständlich
	- Fehlersuche mühsam
	- Code für Aktionen umständlich
- Handgeschrieben
	- wartungsfreundlich, flexibel
	- fehleranfälliger

## Implementierungsdetails

### Stringtabelle
- Enthält alle Bezeichner
- Information, ob Bezeichner Schlüsselwort ist
- Perfekte Hashtabelle bauen, man kennt ja alle Einträge im voraus
- \+ Stringvergleich vermieden (nur Hash vergleichen)
- \+ Platz gespart (Mehrere ids bilden auf 1 Eintrag ab)


### Tokenstrom

Token = (Key, Value, Position) = z.B. `(<id>, myint, 5)`

- *Push-Parser* => Lexer ruft bei erkanntem Token Parser auf
- *Pull-Parser* => Parser ruft Lexer auf
	- übliche Methode, da im Lexer weniger Zustand nötig als im Parser
- Praxis aus den 60ern: Tokens in Dateien abspeichern (HDD)
	- heute z.T. wieder aufgenommen (diesmal in RAM)
	- ist heute wohl so das Schnellste

#### Pufferung
Einlesen ist Hauptaufwand im Lexer, also:

- Aufteilen in Threads

- Double Buffering
    + erster 4kB Block wird gelext, zweiter 4kB Block wird gelesen. War früher wichtig, da IO am langsamsten
        * ! Maximale Vorschau auf Buffer beschränkt


## Ausnahmen

- Ignorierte Blanks: in Fortran 77 (um Platz zu speichern heh)
    + `DO 10 I = 1.5` ist Zuweisung an Variable `DO10I`
    + `DO10I = 1,5` ist Schleifensteuerung
    + => Gesamte Anweisung lesen und Testen auf `=`, etc...

- Signifikante Blanks: zB in Python
    + => Indent-/Dedent-Token um kontextfrei zu bleiben

- Phasenabhängigkeit = Lexer und Parser sind abhängig
    + Letztes Token bei `vector<vector<int>>` ist  `>>`, aber Parser erwartet `>`, `>`
    + => Parser zerlegt `>>` bei Bedarf in zwei Token

- Mehrdeutigkeiten
	- Lexer muss Parser befragen nach syntaktischem Kontext wg Tokenmehrdeutigkeiten
	- Automat hat mehrere Startzustände
	- Parser kann entscheiden, in welchem Startzustand der Lexer anfängt
	- => manche lexikalische Mehrdeutigkeiten aufgelöst (geht nur bei Pull-Parser)



# Syntaktische Analyse

- [Why did GCC switch from Bison to a recursive descent parser for C++ and C?](https://programmers.stackexchange.com/questions/254694/why-did-gcc-switch-from-bison-to-a-recursive-descent-parser-for-c-and-c)

  > [because] error messages are more meaningful when using recursive descent techniques

- [Do modern languages still use parser generators?](https://programmers.stackexchange.com/questions/250256/do-modern-languages-still-use-parser-generators?lq=1)

  > GCC use hand-written parsers in particular to improve syntactic error diagnostics

  > Dealing with that context sensitiveness in grammars for parser generators (i.e. bison or even menhir) is boringly difficult.

## Grammatiken
$$G = (T, N, P, S)$$
: - Terminale = Tokens $$T$$

    - Nichtterminale $$N$$
    - Produktionen $$P$$
    - Startsymbol $$S$$

- Links-/Rechtsableitung
- reduzierte Grammatik = alle NT erreichbar
- deterministische Grammatik = 1 Eingabe <=> 1 Syntaxbaum
- Resultat der Ableitung heißt *Wort* oder *Satz* (odeeerr *Programm*? Er weiß auch nicht)


### Grammar Engineering
Mehrdeutige Grammatiken sind böse (zB *Dangling Else*), deswegen

- Faustregeln
    + 1 NT pro Prioritätsebene
    + Nicht 2x dasselbe NT auf der rechten Seite
- Eindeutig machen:
    - Sprache ändern und ausfaktorisieren (nervt) oder..
    - sicherstellen, dass Parser richtig wählt (in Praxis)
- $$\epsilon$$-Produktionen kann man eliminieren, aber praktisch unwichtig und unnötig

### Konkrete und abstrakte Syntax

|                               |          konkrete Syntax         |                            abstrakte Syntax |
|-------------------------------|--------------------------------|-----------------------------------------------|
| Strukturinfo                  | explizit: `(`, `)`, `begin`, ... |                    implizit in Baumstruktur |
| Ketten-/Verteilerproduktionen |             notwendig            | überflüssig, da keine semantische Bedeutung |
| Schlüsselwörter               |             notwendig            |             überflüssig/implizit in Knoten  |

- Regeln für abstrakte Syntax (das lass ich nur hier stehen, weil ichs schon abgetippt hatte):
    + `=`-Regeln wandeln Knoten in anderen um, aber erzeugen keine Unterbäume
    + `::`-Regeln sind *Baumkonstruktorregeln*
    + `StmtList :: Stmt +`: das `+` bedeutet variable Anzahl von Unterbäumen
    - Variantenbildung mit OO ausdrücken (`inherits`, Konstruktoren)
    - bei `Val` wird Wert direkt in Knoten geschrieben

![](../../media/abstrakter-syntaxbaum.png)


### Stackmaschinen
$$A = (T, Q, R, q_0, F, S, s_0)$$

- Eingabealphabet/Tokens $$T$$
- Zustandsmenge $$Q$$
- Regeln $$R$$
- Anfangszustand $$q_0$$
- Endzustände $$F$$
- Stackalphabet $$S$$
- Anfangszeichen im Stack $$s_0$$
- Regelformat: $$sqax \to s'q'x$$, wobei
    + Stack $$s, s'$$
    + Zustand $$q, q'$$
    + Token $$a$$ (evtl. $$\epsilon$$)
    + Eingabe und Resteingabe$$x, x'$$
- Endezeichen ist `#` (Beim Compiler: EOF)
- Bei der nichtdeterministische Stackmaschine zählt nur, dass es einen richtigen Weg gibt (dh die Maschine "rät immer richtig" da in der Definition ein $$\exists$$-Quantor vorkommt)


## LL-Parser
- **L**inks nach rechts, **L**inksableitung
- *compare*- und *predict*-Schritte
- LL ist einfacher als LR (LR nur mit Generatoren)

$$LL(k)$$-Grammatik
: Aus den nächsten $$k$$ Zeichen kann (unter Berücksichtigung des Stackinhalts) die nächste anzuwendende Produktion eindeutig vorhergesagt werden. Oder in griechischen Buchstaben:

    $$S\Rightarrow^*_L
      \left\{ \begin{array}{l}
                \mu A\chi\Rightarrow_L \mu \nu\chi \Rightarrow^*_L \mu \gamma \\
                \mu A\chi'\Rightarrow_L \mu \omega\chi' \Rightarrow^*_L \mu \gamma'
              \end{array} \right. $$

    Wenn nun $$\{k : \gamma\} = \{k : \gamma'\}$$ so ist $$\nu = \omega$$.

$$SLL(k)$$-Grammatik
: Wie oben, nur dass die Vergangenheiten/Stackinhalt[?] unterschiedlich sein dürfen ($$\mu \neq \mu'$$ erlaubt).

$$k$$-Präfix
: erste $$k$$ Zeichen von Terminalfolge, aufgefüllt mit $$\verb§(#)§$$.

    Beispiel: $$\{2 : \verb§(abcd)§\} = \verb§(ab##)§$$

$$\text{First}_k(x \in V^*)$$
: Alle $$k$$-Präfixe, die sich aus $$x$$ ableiten lassen

    1. Wenn $$\gamma \in T^*$$, dann $$\text{First}_1(\gamma) = \{1:\gamma\}$$.

    2. Wenn $$X \to \alpha \in P$$, dann $$\text{First}_1(\alpha) \subseteq \text{First}_1(X)$$

    3. Wenn $$\alpha \Rightarrow^* \epsilon$$, dann $$\text{First}_1(\beta) \subseteq \text{First}_1(\alpha\beta)$$

    4. $$\text{First}_1(\alpha)\backslash \{\verb§(#)§\} \subseteq \text{First}_1(\alpha\beta)$$


$$\text{Follow}_k(X \in N)$$
:  Alle $$k$$-Präfixe, die direkt rechts von $$X$$ auftreten können

    1. $$\verb§(#)§ \in \text{Follow}_1(S)$$

    2. Wenn $$X \to \alpha Y \beta \in P$$, dann $$\text{First}_1(\beta)\backslash \{\verb§(#)§\} \subseteq \text{Follow}_1(Y)$$

    3. Wenn $$X \to \alpha \in P$$, dann $$\text{Follow}_1(X) \subseteq \text{Follow}_1(\alpha)$$

    4. Wenn $$\beta \Rightarrow^* \epsilon$$, dann $$\text{Follow}_1(\alpha\beta) \subseteq \text{Follow}_1(\alpha)$$

    5. $$\text{Follow}_1(\alpha\beta) \subseteq \text{Follow}_1(\beta)$$

$$SLL(k)$$-Bedingung
: $$G \in SLL(k)$$ genau dann, wenn für alle Produktionen $$A \to \alpha \vert \alpha'$$ gilt, dass die ersten $$k$$ Zeichen von dem, was nach den $$\alpha$$'s kommt nicht gleich sind. Oder:

    $$\text{First}_k(\alpha\text{Follow}_k(A)) \cap \text{First}_k(\alpha' \text{Follow}_k(A)) = \emptyset$$

    Spezialfall: Für $$SLL(1), \, \alpha \not\Rightarrow^* \epsilon, \, \alpha' \not\Rightarrow^* \epsilon$$ muss nur gelten

    $$\text{First}_1(\alpha) \cap \text{First}_1(\alpha') = \emptyset$$

    und falls $$\alpha \Rightarrow^* \epsilon$$ zusätzlich

    $$\text{Follow}_1(A) \cap \text{First}_1(\alpha') = \emptyset$$

Ein Beispiel für eine $$G \notin LL(1)$$, $$G \notin SLL(2)$$ aber $$G \in LL(2)$$ ist gegeben mit den Regeln $$S \to \verb#aAab# \vert \verb#bAbb#$$ und $$A \to \epsilon \vert \verb#a#$$.

### Linksfaktorisierung
Folgende Grammatik ist für kein $$k$$ $$LL(k)$$.

$$S \to X$$

$$X \to Yc \vert Yd$$

$$Y \to a \vert bY$$

(Ein Wort ist dann sowas wie $$\verb§bbbbc§$$ => unendliche Vorausschau)

=> gemeinsamen Anfang $$X$$ ausklammern:

$$X \to YX'$$

$$X' \to c \vert d$$

### Linksrekursion

- Linksrekursive kfG sind für kein k $$LL(k)$$
- z.B $$S \to Sx \vert y$$
- Kann man immer eliminieren

## Tabellenbasierte LL-Parser
- Stack enthält T oder NT
- Tabelle LL[oberstes Stackelement $$X$$, aktuelles Eingabezeichen $$a$$]
    - $$LL[X, a] = X \to X_1 \dots X_n \in P$$, falls $$a \in\text{First}_1( X \to X_1 \text{Follow}_1(X))$$, sonst Fehler
    - Mehr als 1 mögl. Produktion => kein $$LL(1)$$
- Konstruktion mit $$\text{First}_1( X \to X_1 \text{Follow}_1(X))$$ Mengen
- Zur Konfliktlösung bei *Dangling Else* Produktion manuell aus Tabelle entfernen
    - => else gehört immer zum letzten if

![](../../media/table-based-llk-parser.png)

### $$LL(1)$$-Parserprogramm
```c
push('#', S);
t = next_token();

while (true) {
    e = pop();

    if (e in T) { // Vergleiche
        if (t != e)
            abort(e + " expected");
        if (stackEmpty())
            break; // Akzeptiere
        t = next_token();
    }
    else { // Vorhersage
        entry = LL[e, t];
        if (entry == ERROR)
            abort("illegal symbol " + t);
        (X → X_0 ... X_n) = entry;
        push(X_n, X_n-1, ..., X_0);
    }
}
```

## Rekursiver Abstieg

----

TODO TODO TODO TODO TODO TODO (bis LR):

- Für jedes NT eine `parseNT()`-Funktion
- Stack implizit in Funktionsaufrufhierarchie
## Fehlerbehandlung

- Fehler kann erst gefunden werden bei parserdefinierter Fehlerstelle
    - Wenn alle weiteren denkbaren Fortsetzungen nicht mehr zur Sprache gehört

- Solange Tokens überlesen bis wieder in definiertem Zustand (Zeichen aus Ankermenge)
    + Schwierig: Ankermenge definieren
- Korrektur
    + lokal/globale Korrektur
    + heute: einfach nochmal kompilieren


### Ankermenge
- naiv
    + nicht so toll
- systematische Konstruktion
    + Erste Annährung aus Follow-Menge Ank(A) = Follow(A)
        * nicht ausreichend [Beispiel]
    + Erweitere Ank um Anfänge von allen übergeordneten Strukturen (s. Optimierung aus rekursiver Abstieg)
        * immernoch zu ungenau [Beispiel]
- Kontextsensitive Ankermengen
    + Erweiteren um First-Mengen von dem was danach kommen kann + alle weitere Terminale
    + Ist noch in SLL, weil Fehlermeldungen nichts mit eigtl. Parsing zu tun haben
    + Nicolas Wirth - beste Methode bis heute

#### Verwendung

`except`

- Mehrere `except`-Aufrufe hintereinander werfen Fehler
    + => wahrscheinlich Folgefehler
    + Variable `error_mode = true` setzen, um Folgefehler zu erkennen



## LR-Parser (Knuth)

- **L**inks nach rechts, **R**echts (= umgedrehte Rechtsableitung)

- Sobald RHS auf Stack vollständig, ersetzen mit der LHS

- Entscheidung bei LR (Reduce) wird später getroffen als bei LL (Expand)
    + LR funktioniert auch wenn 2 verschiedene T auf die gleiche RHS abgeleitet werden

- Stack wächst wenn ein Token kommt (bei LL schrumpft er, wenn ein Token kommt)

### Komponenten
![](../../media/lr-components.png)

- LR-Parserprogramm
- Stack
    - Zustände
- ACTION-Tabelle
    + Übergänge mit T
    + leerer Eintrag: Syntaxfehler
- GOTO-Tabelle
    + Übergänge mit NT
    + leerer Eintrag: wird nie angefasst (=> Tabellenkompression, heute nicht mehr so wichtig)

![](../../media/lr-automat.png)

- Übergänge
    + Übergangsketten entsprechen genau RHS einer Produktion
    * tatsächlich sind Übergänge alle denkbaren RHS-Ketten
    * Überlappungen werden in Zuständen unterschieden (mithilfe des Eingangs)
    * ist nondeterm. diesen nondeterm Stackm. zu determ umwandeln
- Zustände
    + . gibt an, wo man in der Produktionen gerade ist
    + Reduktionszustände haben keine ausgehenden Pfeile
    + oberer Teil: Man ist in einem dieser Zustände
        * Mehrdeutigkeit aufheben mit Follow-Mengen

![](../../media/lr-parser-table.png)

- Shift X
    - $$qt \to tq$$
- Reduce X
    + $$x_1 \dots x_n q \to Xq$$
    + RHS löschen
    + LHS pushen
        + Zustand = ablesen aus GOTO-Tabelle von vorherigem Zustand
    + Bei $$\epsilon$$-Regel wächst der Stack



#### LR(1)-Parsealgorithmus

```c
push(0);
t = next_token();

while (true) {
    int state = top();
    switch (ACTION[state, t]) {
        case Shift z:
            push(z);
            t = next_token();
            break;

        case Reduce r:
            (A → b) = productions[r];
            for (i = 0; i < len(b); ++i) {
                // b runterschmeissen
                pop();
            }
            // In GOTO-Tabelle von letztem Zustand nachschauen
            push(GOTO[top(), A]);
            break;

        case Accept:
            return;

        default:
            abort("parse error");
    }
}
```

Situation $$\alpha \cdot \beta$$
: Gibt Fortschritt beim Parsen an

    - $$\epsilon$$-Produktion ist einfach nur $$X \to \cdot$$
        - davon nicht verwirren lassen

Nochmal: Übergänge mit NT braucht man um in vorherigen Zustand zu kommen

### Automatenkonstruktion

- Notes:
    - Automat erkennt rechte Seiten (=> lokal)
    - Stack ist da für kontextsensitive [?] Strukturen (=> global)
    - $$LR(0)$$ => schaut kein Input an?
        - Nein, da letztgelesenes Token immer auf Stack und dadurch gelesen
        - Es werden nur keine zusätzlichen Tokens angeschaut
        - Vergleich: $$LL(0)$$-Sprachen bestehen nur aus einem Wort

#### Nichtdeterministischer endlicher Automat
![](../../media/lr-deterministic-automaton.png)

- $$\epsilon$$-Übergänge: wenn ich vor einem $$\cdot X$$ stehe, dann stehe ich vor einer rechten Seite von $$X$$.
    - Zu all diesen Situationen einen Übergang zum NT machen
- Alle Zustände reinschreiben, die mit $$\epsilon$$-Übergängen erreichbar sind

#### Algorithmus

##### Zustände berechnen
- $$\operatorname{closure}(M)$$-Operation
    - Rekursiv Produktionen von NT hinzufügen vor denen ein Punkt steht
    - Fixpunktiteration

![](../../media/closures.png)

##### Übergänge berechnen
- $$\operatorname{GOTO}(I, x)$$-Funktion
- Überall da wo Punkt vor $$x$$ ist den Punkt eins weiter schieben
- Davon $$\operatorname{closure}$$ berechnen
- Fixpunktinteration

![](../../media/lr-goto-function.png)


### Simple LR ($$SLR$$)

- Probleme von $$LR(0)$$
    - *Shift-Reduce*-Konflikte
    - *Reduce-Reduce*-Konflikte

![](../../media/shift-reduce-conflict.png)

- Reduzieren genau dann, wenn Terminal $$\not\in$$ Follow-Menge
- Wenn nicht eindeutig unterschieden werden kann, ist die Grammatik nicht $$SLR$$

### Rechtskontext
- SLR ist zu schwach, weil $$\operatorname{Follow}$$ zu allgemein ist
- => "kontextspezifische" $$\operatorname{Follow}$$-Menge pro Regel (der *Rechtskontext*)
- Reduzieren, genau dann wenn Token in Rechtskontext
- Berechnung bei $$\operatorname{Closure}$$-Berechnung bei

    $$[X \to A \cdot BC ; \alpha]$$

    mit

    $$\operatorname{Rechtskontext}(B) = \operatorname{First}_k (C\alpha).$$

- Tokenmenge die tatsächlich hnter $$B$$ kommen kann
- Trennschärfer, da $$\operatorname{First}_k (C\alpha) \leq \operatorname{Follow}_k (B)$$.
- Rechtskontext kann man einfach übernehmen, wenn nur Punkt verschoben (bei $$\operatorname{GOTO}$$-Funktion)


$$LR(k)$$-Grammatik
: $$S\Rightarrow^*_R
      \left\{ \begin{array}{l}
                \mu  A \omega  \Rightarrow \mu  \chi   \omega  \\
                \mu' B \omega' \Rightarrow \mu' \gamma \omega'
              \end{array} \right. $$

    Wenn nun

    $$(\vert\mu\chi\vert + k) : \mu\chi\omega = (\vert\mu\chi\vert + k) : \mu'\gamma\omega'$$ so ist $$\mu = \mu', \ A = B, \ \chi = \gamma.$$

    - Zum Verständnis vorstellen, dass $$\vert\mu\vert = \vert\mu'\vert$$. Es würde sowieso gelten, dass $$\mu \neq \mu'$$ falls das Kriterium nicht gilt.

    - Berücksichtigt Vergangenheit (müssen gleich sein)

![](../../media/lr-tables.png)

- Achtung: $$v \in V$$ (Tippfehler) [hä ist das jetzt gefixt oder nicht...]
- $$\vert v \vert \in T^{k-1}$$ vorstellen $$T^{\leq k-1}$$ ist nur Optimierungsgedöns

### Praxis

- Automaten nur automatisch generierbar
- Sehr große Tabellen, sehr viele Zustände

### $$LALR(k)$$
- = Look ahead $$LR$$
- Zustände verschmelzen, wenn eigentlich Items die gleichen sind, Rechtskontexte aber vielleicht verschieden
- Rechtskontexte vereinigen
- Dann isomorph zu $$LR(0)$$-Automat
- etwas schwächer als $$LR$$ (Reduce-Reduce-Konflikte, sind aber viel seltener als Shift-Reduce-Konflikte)


18.5.16
erste 10 min  verpasst

##### S. 231
- Beim Übergänge eintragen muss man rückwärts zurückverfolgen, ob Rechtskontexte erweitert werden müssen

### Tabellenoptimierung S. 236 (nur 5 min drüber geredet)

- Spalten verschmelzen (da meistens dünn belegt)
    - evtl. davor passend verschieben
    - evtl. sind sie teils identisch und Überlappungen treffen auf leere Zeilen

### Fehlerbehandlung S. 243

- Grammatik ist mehrdeutig
- Mehrdeutigkeiten in Tabelle manuell auflösen (vgl. dangling else)
    - "Ich warne Sie aber, das ist supergefährlich! [weil man ganz genau wissen muss, was man tut]"
- Lücken manuell mit Fehlereinträge ausfüllen
    - Hier kommt ein Beispiel:
    - Nachricht: "Schließende Klammer fehlt"
    - Aktion: Shift 3 (= praktisch füge Token ein)
    - Geht nur bei kleinen Grammatiken, sonst zuu viiiel
- Ankermengen S.245
    - [kurz nicht aufgepasst]
    - nicht-Closure, nicht-rekursiv weil sonst Endlosschleife


## Parsergeneratoren

Machen alles, z.B.

- _AST als Datenstruktur_
- Semantische Analyse
- Codeausgabe
- => Single-Pass-Compiler
    - früher wichtiger
    - unübersichtlicher

Wir gucken uns **bison** an.


- Mehrdeutigkeiten aufgelöst durch Tokenpräzedenzen

##### S.257, 258
- semantische Aktionen in geschweiften Klammern
- `$$x$$` stehen für ASTs
- `$$1$$` ist Unterbaum von `modifiers`, `$$3$$` von `identifier` usw.
- `$$` ist Baum der linken Seite
- `{;}` heißt nach `class_body` muss ein `;` kommen.
- Mit `error` Fehler abfragen
- Bläht Grammatik auf aber is halt so

##### S. 260
Konfliktfall

- `bison` gibt shift/reduce-conflict aus
- Standardfall: es wird geshiftet
    - Dann funktioniert z.B. das dangling else so wie man will
    - ahja, siehe S. 261

Publikumsfrage: Kann man aus der Grammatik erkennen ob es LR oder LALR ist?
    - Nö, man muss Automat erstellen
    - Definition mit den Ableitungen kann man nicht anwenden, da es unendlich viele Ableitungen gibt

## Spezialfälle

- Typunabhängiges Parsen
    - => Standardtrick: Parser schaut Typen von Bezeichnern in Symboltabelle nach, kann damit Situationen unterscheiden
    - Heutzutage:
        - Erst alle Deklarationen aufsammeln, dann Statements analysieren [macht semantische Analyse?]

[FORTRAN übersprungen]

- Java, C++ Templates ....


## Komplexität und Sätze
- Ableitungsschritt $$O(1)$$
- Parsen $$O(n)$$, $$n$$ ist die Größe des AST

- Falls LR(k) gibts auch LR(1) Grammatik
    - "Aus praktischer Sicht: Finger weg! Das Ding das da rauskommt ist ein Monster."

- LR(k) ist echt stärker als LL(k)

- Starke Aussagen über die Sprachen



## Early Parser

[kommt nicht in der Prüfung dran?]

[hier fehlen ganz oft EOFs]

- für nichtdeterministische Grammatiken
- ist dafür langsamer: $$O(n^3)$$ für n. determ.
- benutzt DP
- Compiler braucht immer nichtdeterministische Grammatik
    - => na toll
    - aber häufig in Computerlinguistik
    - Statistischer Ansatz ist heutzutage trotzdem besser


### Algorithmus

top-down Verfahren

- Prediction
    - vgl. closure
    - Neue Situationen in gleicher Spalte einfügen
- Completion
    - Anfang von A beginnt in Spalte j => In Spalte j gibt es ein Item, wo Punkt vor A steht.
    - Punkt hinter NT schieben und in Situation i einfügen
    - k ändert sich nicht, aber das gehört ja zu dem Anfang von X und ändert sich nicht
    - vgl. reduce
- Scanning
    - $$\verb§(a)§$$ stehe in Spalte $$i$$. Wenn irgendwo Punkt vor $$\verb§(a)§$$ dann den Punkt eins weiterschieben (vgl. shift) und in Spalte $$i+1$$ eintragen.

- Notes:
    - Statt Rechtskontext Spaltennummer


## Beispiel
...

- Man sieht nicht wie die ASTs erzeugt werden und dasss ist nicht so einfach, deswegen lässt er die Erzeugung weg (Bei Scan Blatt, Bei Completion inneren Knoten + Bäume, aber wie Alternativen zusammenbasteln? egal)




# Semantische Analyse

Aufgabe: AST anreichern mit...

- Namensanalyse: Für jeden Bezeichner Verbindung von Verwendung zu Deklaration
    - geschachtelte Blöcke, geschachtelte Klassen => kompliziert, mit Symboltabelle
- Typanalyse: Zu jedem Knoten Typ vermerkt
- Operatoridentifikation: Bei Überladung (`+` ist gleichzeitig Integer-, Double- und Float-Addition)
- Konsistenzprüfung: zyklische Vererbung, in jeder Sprache noch 1000 extra Dinge
- Miteinander verschränkt => Zweipass


## Attributgrammatiken

Don Knuth strikes again!

- Jeder Knoten im AST hat *Attribute*
- Manche initialisiert vom Lexer oder Parser
    - z.B. Wert von Konstanten
- Attributberechnungen werden Produktionen zugeordnet
- Reihenfolge ist wichtig wg. Abhängigkeiten
    - Sonst inkonsistent

### Beispiele
- [S. 296]
    - `.node` = immer noch der erzeugte AST
    - `.in` = Hilfsattribut um linke Hälfte des Baums zwischenzuspeichern
    - vgl. Implementierung rekursiver Abstieg

### Grundbegriffe
Attributgrammatik
: $$(G, A, R, B)$$

    - $$G$$ Grammatik
    - $$A$$ Attribute: vereinigte Menge über alle Grammatiksymbole
        - sind disjunkt, auch wenn sie gleich heißen, da sie woanders zu gehören
    - $$R$$ Attributierungsregeln
        - $$X_i.a := f(\dots, X_j.b, \dots) \in R(p)$$
        - $$X_i, X_j$$ kommt aus Produktion $$p$$, ist T oder NT
    - $$B$$ Bedingungen
    - $$\AF(p) := \{ X.a \, \vert \, X.a := f (\dots) \in R(p) \}$$ heißt *Menge der in $$p$$ definierten Attribute*

Synthetisiertes Attribut
: $$\syn(X) = \{ X.a \, \vert \, \exists p : X \to X_1 \dots X_n \in P \wedge X.a \in \AF(p) \}$$

    Sind rein bottom-up ausrechenbar.

Ererbtes Attribut
: $$\{ X.a \, \vert \, \exists q : Y \to \mu X \omega \in P \wedge X.a \in \AF(q) \}$$

    Attribut steht in LHS und RHS.


- $$\syn(X)$$ und $$\inh(X)$$ sind disjunkt für alle X im Vokabular von $$G$$
- Für alle $$a \in A(X)$$ existiert nur eine Berechnungsregel $$\in R$$

[S. 308]

vollständige AG
: - Für alle Attribute, die nicht vorbesetzt sind, gibt es eine
Attributierungsregel in der "richtigen" Produktion (linksseitig bei
abgeleiteten, rechtsseitig bei ererbten Attributen)

    - Die Baumwurzel besitzt keine ererbten Attribute

    D.h.

    - Für alle $$p : X \to x \in P$$ ist $$\syn(X) \subseteq \AF(p)$$
    - Für alle $$q : Y \to \mu X \omega \in P$$ ist $$\inh(X) \subseteq \AF(q)$$
    - $$\syn(x) \cup \inh(X) = A(X)$$
    - Für das Startsymbol $$Z$$ ist $$\inh(Z) = \emptyset$$

korrekt attributierte AG
: wenn Attribute konsistent belegt werden, so dass alle Attributgleichungen korrekt erfüllt sind

    todotodotodotodotodo

Direkte Attributabhängigkeit
: todotodotodotodotodotodo

Lokal azyklische AG
: todotodotodotodo


### LAG(1)

- Links abwärts bzw linksassoziativ
- Abhängigkeiten in DFS-Reihenfolge
- Während LL-Parsens auswertbar
- Implementierung mit rekursivem Abstieg oder tabellengesteuert
- [S 316] Geht bei LR iwie auch aber wurde nicht erklärt (er findet es zu gruselig)

### LAG(k)
- k-mal LAG auswerten
- Wenn Attribute mehrere Schritte zur Auswertung brauchen
- LAG(1) reicht fast nie aus

### OAG

Weggelassen weil keine Zeit, sind schick aber in Praxis nicht so doll (auch heute nooch zu langsam und zu viel Speicher)

S. 318 - S. 329 übersprungen

## Namensanalyse

- Scope-Auflösung
- *Ref*-Variablen sollen Attriut *decl* haben, dass auf zugehörige Deklaration im AST verweist

[15 min gefehlt S.329 - S. 336]

S. 336

- in echt eine globale umg-Tabelle (genannt die Symboltabelle), sonst zu viel Kopiererei
- Reihenfolge von Decls wichtig, wegen local vs global scope und so
    - pro Block einen eigenen balancierten Baum
    - Blöcke [Scopes?] in Stack angeordnet

#### S. 339 Beispiel AST
- umg wird weitergereicht
- => Zugriff auf voangegangene Bezeichner

????

sorry, future daniel

### Namespaces

- nicht geschachtelt
- neues Attribut `decls` ist Umgebung die zum benannten Gültigkeitsbereich gehört
- Umgebung hängt dann an Bezeichner anstatt an Block

## Typanalyse
- Hängt mit Namensanalyse zusammen
- Berechnet Typ von Operationen
- Konsistenzprüfung
- implicit casting

"Trivialbeispiele" weggelassen bis inkl. S. 351

- Namensgleichheit
    - = gleicher Name
    - Java: Klassen, immer verschieden auch wenn exakt gleicher Inhalt
- Strukturgleichheit
    - = gleiche Struktur
    - Haskells Typen (Typinferenz mit Unifikation)
        - hat aber auch Namensgleichheit

- Zwei Attribute pro Knoten
    - vor: kommt von unten, Typ des Ausdrucks
    - nach: kommt von oben, erwarteter Typ
    - wenn nicht gleich muss gecastet werden
- Für jeden Knoten müssen Attribute gesetzt sein für Codegenerierung

1.6.16

[S 356]
Allg. lassen sich Typisierungsgleichungen so wie sie sind 1a implementieren, d.h. `vor`- und `nach`-Attribute als Knoten im AST drin



## Namens-Typ-Analyse
Namens-/Typanalyse voneinander abhängig
- `MyClass obj` - Welcher Typ hat obj?
- `sin(x)` - Ist x double oder float?
- `obj.m()` - Welche Methode wird aufgerufen?

[S 361]
Zusätzlich nur: Typ der Initialisierungsexpression wird geprüft mit `Expr.nach = Type.decl`

### Überladung
- Menge von Deklarationen zurückgeliefert
- Auswahl aus Menge mit Typinformation

#### [S. 363] [Z]
1. Typ von `op1 t op2` übernehmen
2. Deklarationen von `t` finden
3. Eingrenzen mit Typinformation
4. `nach`-Typ von `op1`, `op2` ausrechnen
5. Typanpassung ... steht eigtl alles auf Folien
6. ...
7. Operatortyp kann auch von verlangtem Ergebnistyp abhängen (Haskell), dann doppelter Baumdurchlauf

### Typ-definierte Namensräume
- Bisher explizite Namensräume `NS::a`
- Jetzt auch für Klassen, Records, Module


## Praxis
- Meistens von Hand (z.B. GCC, LLVM, javac)
- LAG(1) mit Visitor-Pattern
- Mit AGs und Generatoren hat sich nicht durchgesetzt
    - obwohl Zyklentest dabei
    - aber Performanzprobleme mit `umg` und so
    - AST kann auch nicht umgebaut werden (für Transformation und Optimierung)

### Symboltabelle
- Schachtelung von Namensräumen
    - => Symboltabelle als Stack organisieren
- Struktur der Symboltabelle ist immer LAG(1), wenn man Block verlässt braucht man Tabelle nie nicht mehr auch wenn der Rest LAG(500) ist
    - Weil man immer erstmal auf dem Block bleibt [???]
- In echten Compilern steht auch der Typ, Größe in Byte, Ausrichtungsconstraint, etc. zusammen mit Bezeichner in Symboltabelle
- Friedrich Ludwig Bauer ist über Doktorand Snelting auf einer Sommerschule hergefallen, als er Orgel spielte und sagte er solle die Orgel nicht kaputt machen
- Optimierung:
    - Bezeichner in Stringtabelle speichern
    - dort Verweis auf aktuelle gültige Deklaration speichern
    - Dafür muss man die Deklarationen immer aktualisieren
    - So viel schneller ist das heute auch nicht mehr

- Implementierung von benannten Namensräumen (zB Klassen)
    - In Typ Verweis speichern auf weitere lokale Symboltabelle von Klasse, die nicht stack-basiert ist
    - "Punkt-Operator öffnet benannten Gültigkeitsbereich"
    - Man kann Stack um lokale Symboltabelle erweitern (Also nur Stack von der Variable, nicht ganzer Block)
    - geht in Java z.B. nicht weil Punkt-Operator da iwie so funktioniert, aber in Pascal mit with-Anweisung geht's schon, weiß aber auch nicht wieso genau jetzt



# Transformation

2.6.16



## Zwischensprachen
Wieso Zwischensprachen? -- Nur n+m statt n*m Frontend-Backend-Kombinationen notwendig. Außerdem:

<table>
<tr>
<th>Vorteil</th>
<th>Datentypen orientieren sich an</th>
<th>Weiterverwendung</th>
<th>Beispiele</th>
</tr>
<tr>
<td>Optimierungen einfacher</td>
<td>Quellsprache</td>
<td>keine (nur während Kompilieren)</td>
<td>GCC, LLVM</td>
</tr>
<tr>
<td>Plattformunabhängiger</td>
<td>Zielsprache</td>
<td>zum Austausch geeignet (<code>.class</code>)</td>
<td>Java, .NET, Dalvik</td>
</tr>
</table>

### Typen

<table>
<tr>
<th>Typ</th>
<th>Werte liegen in...</th>
<th>Operationen arbeiten auf...</th>
<th>Ablaufsteuerung</th>
<th>Erzeugung</th>
<th>Geeignet für</th>
</tr>
<tr>
<td>Stackcode</td>
<td>Stack</td>
<td>oberste Stackwerte</td>
<td rowspan="2">(bedingte) Sprünge</td>
<td>einfacher</td>
<td>Interpreter</td>
</tr>

<tr>
<td>Registercode</td>
<td>Register (meist unbeschränkte Anzahl)</td>
<td>explizite Register</td>
<!-- <td>(bedingte) Sprünge</td> -->
<td>schwieriger</td>
<td>Optimierung, explizite Codeerzeugung</td>
</tr>
</table>

Es gibt einen Compiler für Java-Bytecode -> Dalvik aber nicht umgekehrt => Beispiel dafür, das Registersprache komplizierter ist [naja!]

### Java-Bytecode

- Stackcode
- Instruktionen: viele

[S ~389 - 392 übersprungen]

### Registercode
- Befehle arbeiten auf Register: `r1 = add r2, 1`
- `r1 = load[r2 + k]`: in `r2` steht Adresse, Offset von k
    - Er redet 3 Minute darüber, dass das das wichtigste und fundamentalste Konstrukt of all time für Compilerbau ist (dass man nen Offset hat)
- unbeschränkte Registerzahl
    - alle gleich groß (homogen)
    - auf allen kann man alles machen (floating point operations etc)
- näher am Maschinencode als Bytecode, aber immernoch Distanz

Umgekehrte polnische Notation
: Zuerst Operanden dann Operation.

    z.B. `a = a + 1` ➔ `a a 1 + =`

    Subba für Stackmaschinen

UPN -> Bytecode mit Postfix-Durchlauf, Befehlserzeugung bei Verlassen eines Knotens, tada!

### Programmstruktur

Grundblock
: maximale Folge von Befehlen, die alle zusammen ausgeführt werden

    - beginnt mit Sprungmarke
    - endet mit Sprung zum nächsten Grundblock
    - [...]

    Darstellung: Kontrollflussgraph ("Diese Art von Diagrammen behandelt man meines Wissens schon im Kindergarten")

    - Richtigkeit=Vollständigkeit im CFG nicht so einfach (weil man an alle Möglichkeiten denken muss)

## kp wie die Überschriften hier grad geschachtelt sind

## Abbildung der Ablaufsteuerng
- nach `if`-Zweigen immer Sprung ans Ende
- todo Bildchen aus Skript kopieren für for, while, blablablaaahrrggg

[ . . . ]

[S 413]
## LAG(1) für `while`
- dadurch Kopplung an AST
- `.code` ist erzeugter Bytecode
- `+` ist string concat
- `newLabel` erzeugt unique Label
 `.loc` Label am Anfang
 - `.next` Label zum nächsten Block
 - `WhileStmt.next` ist ererbt
- wo sind die `goto`s? => kommen später




8.6.16

[S 417]

- `.fall` gibt an, ob Code für true-Fall oder false-Fall direkt nach Code für Expression
    - => kein `goto` notwendig

- Kontrollfluss is ziemlich billig

## Typabbildung
- Abbildung auf addressierbaren Speicherbereich
- => Größe?
    - Boolean = theoretisch 1 Bit, praktisch 1 Byte
- => Ausrichtung?
    - ARM: nicht ausgerichtet illegal
    - x86: nicht ausgerichtet langsamer


### Primitive Typen
- Zwischensprachen orientieren sich an üblichen Datentypen (int: 8, 16, 32, 64 bit; float: 32, 64 bit; vector [SIMD?]: 128+ bit)
- Unnötige Abbildungen vermeiden (z.B. 64bit-Typ auf 32bit-Architektur)


### Zusammengesetzte Typen
- Arrays: statisch, dynamisch, flexibel
- Deskriptor für Speicherinfo

### Arrays

- Wir nehmen row-major an
- adr(a[0] oder bei mehr Dims a[0,...,0]) ist Anfangsadresse

$$\adr(a[i]) = \adr(a[0]) + di$$

Zugriff auf $$a[u_1 \dots o_1, u_2 \dots o_2]$$: $$\adr(a[i, j]) = \adr(a[0,0]) + d(i(o_2 - u_2 + 1) + j)$$

$$\adr(a[i_1, \dots , i_n]) = adr(a[0, \dots, 0]) + d \sum^{n}_{v=1} \left( i_v \prod^{n}_{\mu = \nu + 1} l_\mu \right)$$

[S 430]

- load[adr(a[0,0,0])] ist Anfangsadresse aus Speicher

### Records

[S 433]

- ...
- `align(offset, alignment)` alignt auf nächsten gültigen offset
- `layout(record)` tjo


## Speicherorganisation und Funktionsaufruf

- früher (FORTRAN 77) nur statische Abbildung
    - zu restriktiv (z.B. mehrere Programme laden, Rekursionen, ...)
- deswegen braucht man ARs

### Activation Records (Stack Frames)
- Enthält nötige Daten für Funktionsabarbeitung
- Pro gleichzeitig aktivem Funktionsaufruf 1 Instanz

Activation Record
: - Funktionsargumente
- Rückgabewert
- Dynamic Link (Verweis auf Vorgänger-AR)
- Static Link (Verweis auf AR der umschließenden Funktion; für geschachtelte Funktionen, Lambdas und so)
- Rücksprungadresse
- Lokale Variablen

#### Verwendung

- Garantiert: `baseptr` zeigt auf feste Stelle im aktuellen AR
- Um Sachen zu finden addiert man statische Offsets auf `bp` (positive Offsets für Parameter und Rückgabewerte; negative Offsets für lokale Variablen)


#### Speicherlayout AR
- Caller schreibt Argumente gleich hinter dem eigenen AR
- Dyn. Vorgänger ist Pointer auf darunterliegendes Stackelement
- Reihenfolge relativ flexibel
- Argumente haben variable Größe z.b. in C bei `void printf(const char *format, ...)` oder wie das heißt
- Variablen haben variable Größe, falls es dynamische Stackarrays gibt

![AR Speicherlayout](../../media/stackframe-layout.svg)

### Funktionsaufruf
- Mitspieler: Caller und Callee
- Allgemein: möglichst viel im Caller machen
    - => weniger Redundanzpotential

#### Aufruf

- `sp` notwendig für dynamische Arrays/Variablenlisten
    - zeigt auf erste freie Adresse nach aktuellem AR (*Top-Stack*)
    - modern: `push`-Assemblyanweisung pusht auf Stack und erhöht gleichzeitig `sp`
- Rücksprungaddresse := Adresse hinter Sprungbefehl in Funktion, berechnet aus `PC`

Ablauf eines Funktionsaufrufs `foo(42, 23)`:

 Zu Beginn: baseptr (`bp`) zeigt auf
Aufrufer-AR, stackptr (`sp`) zeigt auf
Top-Stack

1. [Aufrufer] Argumente eintragen
3. Rückgabewert: freilassen
4. Dynamic Link = `bp`
5. Static Link = setzt Compiler je nach Schachtelung
6. Return Adress = `PC + 1`
7. Sprung zu `foo`
8. [Aufgerufener] `bp = sp`
9. Lokale Variablen anlegen, läuft los

#### Rückkehr

Zu Beginn: Rumpf der aufgerufenen
Funktion wurde ausgeführt

1. Rückgabewert eintragen
3. stackptr zurücksetzen: `sp = bp`
4. baseptr zurücksetzen: `bp` = Dyn. Link
5. Rücksprung zu Aufrufer:
Rücksprungadresse liegt genau an `sp`
6. Rückgabewert laden, liegt an konstantem
Offset relativ zu `sp`
7. Activation-Record des Aufgerufenen
abräumen: Verschiebung von `sp`, Aufrufer
kennt Anzahl der Argumente
8. Zustand jetzt wie vor dem Aufruf, Aufrufer
läuft weiter

![](../../media/stackframe-function-call.svg)

#### Plattformabhängiges Verhalten

Praktisch wird oft vom allgemeinen Vorgehen abgewichen:

- Funktionsaufruf hardwareunterstützt (`call`, `ret`)
- erste ~4 Argumente in Registern übergeben, nicht in AR
- Unterteilung Register in 2 Bereiche
    - *caller-save* kann von Callee überschrieben werden
    - *callee-save* muss von Callee am Ende wieder in Originalzustand gebracht werden
- Wenn möglich kann `bp` weggelassen werden (z.B. keine dynamischen Stack-Arrays), dann alles relativ zu `sp`
    - spart ein Register
    - vgl. `-fomit-frame-pointer` in GCC

#### Parameterübergabe

Call-by-Value
: Aufrufer übergibt Werte direkt

    - Parameteränderungen bleiben lokal
    - Ineffizient für Arrays etc.

Call-by-Reference
: Aufrufer übergibt Verweise (Adressen)

Call-by-Name
: nur in Lambda-Kalkül und ALGOL. "Text" von Parameter wird übergeben.

    - Implementation mit Makrosubstitution
        - in AR liegt *Thunk*-Adresse: Hilfsfunktion zur Argumentauswertung
        - defacto function pointer
    - Teuer, da jedes Mal Funktionsaufruf

    *Jensen's Device* iteriert über Array mit `A[i]` und `i` als by-name Parameter

Call-by-Value-Result
: Parameter by-value übergeben, erst am Ende in Original zurückkopieren

    - löst *Aliasing*-Problem bei by-Reference


### Geschachtelte Funktionen
Prinzip:

- Problem Shadowing von Variablen
- In Static Link weitersuchen
- Theoretisch solange den Static Links folgen, bis man was findet
- Praktisch kennt Compiler Schachtelungstiefe
- Trotzdem teurer wegen Indirektion => Displays

statische Variablenbindung (static/lexical scoping)
: outer-scope Variablen kommen aus *textueller* Umgebung

    Vorteile:

    - AR können auch in geschachtelten Funktionen benutzt werden
    - Symboltabelle kann als Stack implementiert werden


dynamische Variablenbindung (dynamic scoping)
: outer-scope Variablen sind alle, die zum Zeitpunkt der Ausführung existieren (wahlweise in Lisps).

```c
void p() {
    int x = 42;

    void q() {
        // lexical scoping: x = 42
        // dynamic scoping: x = 17
        writeln(x);
    }

    void r() {
        int x = 17;
        q();
    }

    r();
}
```


#### Closures
- In C, C++, FORTRAN gibt es keine geschachtelten Funktionen => einfach
- Was ist wenn ARs schon weg sind?


### Displays






# Optimierung

watwat

Halbordnung
: $$(M, \leq)$$ ist *Halbordnung* $$\iff \leq$$ ist Äquivalenzrelation (reflexiv, transitiv, antisymmetrisch)

wat wat

Supremum
: $$\sqcup z \iff$$ jedes andere sup ist größer

Infimum
: $$\sqcap z \iff$$ jedes andere inf ist kleiner

das

- Halbordnung $$(M, \leq)$$ ist *Verband* $$\iff$$ für je zwei Elemente $$x, y \in M$$ stets gilt $$x \sqcup y$$ und $$x \sqcap y$$.

- $$(M, \sqcap, \sqcup$$ ist Verband $$\iff$$ *Dinge* gelten

Potenzmengen sind Verbände

S. 516: Quiz: Verband oder Halbordnung?

von links nach rechts und oben nach unten:

V,(flacher) V, (flacher) V, V

V, V, HO, HO (Halbverband, hat inf aber kein sup)

Verband auf den Kopf stellen => dualer Verband

"Distributivität ist für Verbände sehr starke Eigenschaft (dann ist Programmanalyse viel präziser)"

Verband $$(M, \leq)$$ vollständig wenn für beliebige Teilmenge sup, inf (aus $$M$$!) existieren (für endliche Verbände trivial)

Halbverband + neues Element als sup ist immer Verband

Produkt von Verbänden sind alle n-Tupel Kombinationen

Summe: oberstes Topelemente abschneiden und neues als gemeinsames Top nehmen; genauso mit bottom. 2-Tupel $$(i, x_i)$$ benutzen um Verbände zu nummerieren

In Programmanalyse läuft vieles darauf hinaus, einen Fixpunkt ($$f(x) = x$$) für einen Verband zu berechnen

Satz: In Verband endlicher Höhe hat jede Funktion einen kleinsten Fixpunkt und zwar das sup von f(f(f(fbottom))) oder so

Beispiel: LGS Lösung berechnen

$$\llbracket \rrbracket : N \to V $$ weist CGG-Knoten ein Verbandselement zu

naiver Ansatz rechnet einfach bis sich nix mehr ändert, das ist aber sehr langsam

chaotic iteration: Transferfunktionen einzeln anwenden. Weil dann spätere Funktionen schon bessere Werte von vorher bekommen, bevor eine Epoche rum ist. Ist aber immernoch langsam

Worklist-Algorithmus:

```
x1 = bot; . . . xn = bot;
// Worklist q (wie Queue)
q = [v1, . . . , vn];
while (q != []) {
    assume q = [vi , . . . ]; // vi aus q nehmen. normalerweise hängt f_i nur vom vorherigen Knoten ab
    y = fi (x1, . . . , xn); // neuen Wert ausrechnen
    q = q.tail();
    if (y != xi ) { // wenn verschieden, dann müssen Nachfolger auch neu berechnet werden
        for (v in succs(vi ))
        {
            q.append(v);
        }
        xi = y;
    }
}
```
Zyklen machen Konvergenz langsamer. Beschleunigung: Bei geschachtelten Schleifen stabilisiert man erst die Schleife und geht dann erst außerhalb

[Galois-Verbindungen, Begriffsverbände weggelassen]

X dominiert Y, wenn jeder Pfad von Y über X geht. z.b. loop-begin dominiert immer loop-body



22.6.16

# S537

Man kann sich vorstellen der Kreis gibt eine Variable an:

- oranger Kreis ist kompletter Wertebereich (zb 2^64 bei `int`)
- weißer Kreis ist Werte die die Variable in allen denkbaren Programmläufen annehmen kann

Generell sind Eigenschaften unentscheidbar (Satz von Rice)

Compiler muss überapproximieren (konservativ approximieren), da sonst Programme nicht funktionieren



# S541

CFG ist konservative Approximation

Korrektsheitbedingung: Jeder vom Programm durchlaufene Pfad muss im CFG möglich

Darf aber zu viele haben => überapproximiert

Natürlich möglichst wenig => hohe Präzision (für 1-Ein-Ausgang Sachen sind sie präzise)

# S542

Dominanz = von Start zu Y, Postdominanz = von X nach End

# S543

Gute Aufgabe

# S544

direkte Dominatoren sind meisten direkte Vorgänger (wann nicht: z.b. Diamantstruktur s Beispiel C-D-E-F)

# S545


# bis S552

Schleifen, etc. aus CFG rekonstruieren: Dominanzbaum von Schleifenaustrittspunkt rückverfolgen bis zum Dominator (oder so, Stichwort Intervallanalyse?)

GOTO-Elimination möglich, ist aber hässlich

# S553

Dominatoren für alle Knoten ausrechnen => Gleichungssystem mit sovielen Gleichungen wie Knoten. Lösen mit Fixpunkt

# S554 - 564

übersprungen

# S567

u1, u2, u3 sind Konstanten

Auf Datenflussanalyse bauen viele andere Analysen auf

# S572
f_B (im Compilerbau genannt Transferfunktionen) bildet von Potenzmenge auf Potenzmenge ab. Es überlebt der Teil der nicht gekillt wird, logischerweise

Struktur des Gleichungssystem hängt nur vom Graphen ab, der Verband und die Transferfunktionen hängt nur vom Analyseproblem ab

für Potenzmengen eignen sich Bitvektoren super (effizient wg. bitweisem AND und OR) s S576

# S574

15 Minuten dafür...

# S575

bottom ist leere Menge ist Unwissen

in der Praxis rechnet man Elemente in der Mitte zwischen bottom und top aus



23.6.16

# S584

Wieso ist Konstantenpropagation nicht distributiv?
A: Kann keine Algebra

Beispiel mit y = x*x

586 Satz von Kam/Ullman: Fixpunktiteration liefert genau sup von den unendlich möglichen Pfaden zu s. Beweis ist 4 Seiten "den bring ich nie mehr in der Vorlesung" "da waren am Ende dann alle eingeschlafen"
meet (= sup auf Englisch) over all parts - MOP

Gödelnummer ist Kodierung des Programmtextes (also einfach der komplette ASCII-Bitstring als Zahl interpretiert)

Was ist intra-/interprozedural? Was ist besser? Wie kann man die Aufrufstellen unterscheiden?


- ganz einfach: inlining (aber bläht Code auf und geht nicht bei Rekursion)

591

- mit Callgraph
    - Kante Call -> Ret für Seiteneffekte
    - Kontexte: Call Strings (wie Stack Frames)
    - (Funktional nicht behandelt)

Def. Kontextproblem? [Konstantenpropagation]

Wieso Call String Länge begrenzen?
A: Damits schneller ist, und diminishing returns. Wird kaum gemacht


Wie findet man sich gegenseitig rekursiv-aufrufende Funktionen? => alle stark-zusammenhängenden Komponenten in Callgraph finden



[...]

6.7.16

628

- **Gute Übungsaufgabe**
- Alle Definition dominieren jede Verwendung
- Phi-Funktionen sind da, wo SSA-Variable nicht dominiert
    - = Dominanzgrenzen
- 2a. Keine Phi-Funktion für a, weil nur 1 Definition von a
- 2d. iterierte Dominanzgrenzen, die dritte Funktion kommt durch die anderen beiden zustande

629 Aus AST kann CFG und Phi-Funktion direkt berechnet werden (Dominanzgraph braucht man nicht)

bis 659 übersprungen

660 Allgemeine Beschreibung von Datenflussanalysen

- Richtung: bei Rückwärtsanalysen sind alle Kanten vertauscht (und dadurch z.B. Vorgänger statt Nachfolger reingeben), sonst kein Unterscheid
- Wenn Verband auf Kopf steht kann May wie Must aussehen
    - May: Sichtbare Definitionen. Wenn Def. nicht gekillt wird, *kann sie* jeden Punkt erreichen. Wenn 2 zusammenlaufen muss man vereinigen. Es kann aber auch 2 verschiedene Pfade geben, hängt davon welcher Pfad zur Laufzeit genommen wird.
        - may: mit  bottom initialisieren
    - Must: Dominatorberechnung. Dominator *muss* alle Vorgänger dominieren
        - must: mit top initialisieren
- 2*2 Einteilung von Verfahren (must, may; forwards, backwards)

663 Unterschied zu "Sichtbare Ausdrücke finden": Menge sind ASTs und nicht Variablen

- Ist Must-Analyse

665 Loop Invariants, Invariant Code Motion

- Alle sichtbaren Definition von Ausdruck sind vor Schleife => ist loop-invariant
- Ist May-Analyse

668 Induction Variables; Reduction of Strength

- Lieber "ergänze" (und dann gucken ob tot) statt ersetze

670 mit SSA einfach, weil Definition immer dominieren

672 Dead Code = durch konstante boolsche Ausdrücke; hier: dead variables => live variable analysis

- May-Rückwärts-Analyse
- Man könnte hier noch `i` entfernen und stattdessen `t3` benutzen



# Informationsflusskontrolle (IFC)

= Finde alle potentiellen Informationslecks (≡ Nichtinferenz)

> Röntgen statt Passkontrolle

(rationale: Zertifikate können gefälscht werden)



677

Safety
: Programm macht nichts Schlimmes (killt niemanden) => viel Testen oder Verifikation

Security
: Dritte können nichts Schlimmes mit dem Programm tun

Confidentiality ist Integrity wo der Verband auf dem Kopf steht

681

- high = geheim
- low = öffentlich
- physikalische Seitenkanäle: z.B. Stromverbrauch von Kryptochips beobachten

683 Problem: IFC liefert keine Aussage über Schwere des Lecks. In diesem Fall nur Info, dass User/Passwort stimmt

deswegen 684 deklassifiziere Variable oder 685 markiere boolsche Bedingung als deklassifiziert (aber das passt eher zu Programmverifikation)

687 Information darf nicht nach unten weitergegeben werden (no read up / no write down)

689

- ~l heißt low equivalence

- Programme P und Q
- In Q kann Angreifer mit Datenflussanalyse gucken, wie die veränderte Variable im Programm macht

7.7.16

688

c steht für command (Nichtinterferenz auch auf einzelene Anweisungen anwendbar)

Allgemeiner Fall oben weggelassen ..

691 cool stuff we want:

- mathematisch bewiesene Korrektheit
- Präzise Warnungen mit möglichst wenig false-positives
    - trade-off mit Korrektheit
    - Lieber nur 80% der Lecks finden, sonst Analyse zu teuer
        - möglichst wenige Annotations
- skalierbar

695 Non-standard Typsystem weil Typen nicht int, Tupel, etc sind sondern Sicherheitsstufen (2 Typen: high and low)

high-Variable kann low-Wert zugewiesen werden, weil in (R-VAL) erlaubt ist, dass R-VAL low oder high sein kann + Kombination mit (ASS)-Regel

var und cmd stehen hinten damit man die Aussagen unterscheiden kann

[..gutes Beispiel..]

700 Beweise bissl angeguckt. Stichwort: Induktion über syntaktischen Aufbau

706, 707 weggelassen $$\Huge(\mathcal{Z})$$

709 Slicing ist cooler als Typanalyse weil mit Datenflussanalyse

710

(P,R)-sicher
: was reinkommt darf höchstens so sicher sein wie das, was rauskommt

..

717 übersprungen

719 probabilistische Lecks, z.B. nutzbar für Timing-Attacken


13.7.16

[Erste 15 min verpasst]
Ershov-Prinzip
: Immer zuerst Operand mit größtem Stackbedarf berechnen. \\
    Alle Zwischenergebnisse aufheben. dh. für jeden Operand ein Stackeintrag weniger

    Geht halt nur, wenn Operationen kommutativ werden. Deswegen gut aufpassen w.g. overflow und so. Aufpassen tut der Codegenerator aber nicht, das muss der Programmierer machen. Bei Subtraktion Operanden umtauschen für richtige Reihenfolge.

Starkes Normalformtheorem
: Homogenitätsforderung: Alle Operanden und Operationen können alle Register benutzen. Ist in echt nicht so!

14.7.16

...

BUPM:  Von bottom-up aus jeweils günstigste Instruktion auswählen (die größten Teilbaum eliminiert)

## Registerzuteilung

Registerallokation nimmt mit am meisten Zeit in modernen Compiler

812 Registerklassen (gp, fp, sse, ...) und Adressmodi kann man erst richtig zuteilen, wenn schon Maschinencode generiert wurde

Registerzuteilung früher über Ausdrücke, heute über mehrere Grundblöcke (aufpassen bei Schleifengrenzen). Übers Programm wird das nie gemacht


20.7.16


Registerdruck
: Wie viele Register sind an der Stelle dieser Anweisung / dieses Grundblockes belegt

817 primitives Verfahren, wenn nur 2 Register vorhanden (Akku + Hilfsregister): alles in Activation Record schreiben

### Lokale Registerzuteilung mit Freiliste (on the fly)

- Immer nur 1 Block betrachten
- Je nach Bedarf Register aus Freiliste alloc-en
- Nach letzter Verwendung free-en
    - und `STORE` in AR
    - in ganz alten Compilern `STORE` erst am Ende des Blocks
- Spillcode erzeugen, wenn Liste leer

(Für jede Registerklasse eigene List)

821 Erweiterungen nicht behandelt, weil: benutzt eh niemand mehr das Zeug


### Linear-Scan Register Allocation

relativ neu (neuer als Graphfärbung; man brauchte was günstigeres für JIT)

824 Umgekehrte Postfixordnung liefert topologische Sortierung

Lebende Definition (zb aus DaFluAna) nötig, um Lebenszeitintervalle zu finden

Register mit längstem Lebendigkeitsintervall auslagern (Heuristik: dadurch verschwinden wshl die meisten Konflikte)


### Graphfärben

optimal, aber NP-vollständig.

gute Heuristiken

alles in SSA => Graphfärben trivial (made in KA)

Kanten geben Konflikte zwischen Anweisungen an "Keine Kanten der Liebe, sondern Kanten des Krieges!"

Maximaler Registerdruck ist an wenigen Stellen maximal

830 1 Knoten mit 7 Kanten, Rest weniger

Welchen Knoten entfernen? Empirisch einfach irgendeinen aus denen mit den meisten Kanten kleiner gleich k

832 IG-Bauen = Interferenzgraph bauen

833 Bei Grad = genau k kann man das machen, aber wenn Grad > k dann wird die Hoffnung schnell klein, das es doch noch klappen könnte

834 - 837 übersprungen

#### Registerzuteilung mit Graphfärbung (nach Hack/Goos)

840 Beim Untergraph nimmt man immer alle Kanten in der Untermenge der Knoten, Beim Teilgraph sind nicht alle Kanten drin

843 Jeder Zyklus größer 3 muss eine Sehne enthalten

Der letzte ist nicht chordal (Ecken als Untergraph haben Zyklus 4)

844 SSA-IGs sind chordal, bäääm

wieso? in SSA gibt es keine Lücken in Lebendigkeitsintervallen. Eine Lücke wäre genau eine fehlende Sehne, die zu einem Zyklus führt

bis 855 weggelassen (eigentlicher Färbalgo und so nicht wichtig, sondern Prinzip)

856 k-färbbar mit k = Registerdruck, deswegen bei Programmstellen mit Registerdruck > Registerzahl auslagern. Also kann man Spillcode gleich am Anfang überzeugen und weiß, dass es k-färbbar sein wird. Geht nur bei chordalen Graphen

861 Standardverfahren: im Vorgängerblock zusätzliche Kopien anlegen In Spezialfällen braucht man ein Hilfsregister, aber das geht bei zu großem Registerdruck ja nicht

865 man kann auch Registerpermutationen in FPGAs machen, wenn man Bock hat

bis 876 übersprungen


### Befehlsanordnung

Besser vor oder nach Registerallokation? Weiß man nicht wirklich

Befehle so anordnen, dass Pipeline-Interrupts und Cache-Misses verhindert werden

RAW, WAR, WAW Abhängigkeiten (bei Data-Races die gleichen Abhängigkeiten in nebenläufiger Form)

`mov`-Befehle möglichst weit nach vorne ziehen, damit Daten da sind, wenn si weiterverarbeitet werden

884 Standardalgo für topologische Sortierung

885 Den Befehl nehmen, der am nähesten an den 3 Kriterien ist

887 `mov`-Befehle nach vorne ziehen:

- gut: Pipeline-Interrupts verhindern
- schlecht: Registerverbrauch steigtf

889 Beispiel bei Software-Pipelining: Man macht Loop-Unrolling und ordert danach die Befehle um. Bei mehreren Schleifen muss man die *loop carried dependencies* beachten, man kann aber trotzdem ausrollen

890 Man macht quasi aus der for-Schleife eine do-while-Schleife mit einem einmaligen Sprung zum Ende der Schleife(/zum Schleifenkopf/-schwanz)

### Peephole-Optimierung

892 Mit BURS und co nicht mehr sooo relevant

Die Beispiele sind ein bisschen bescheuert, aber frühere Compiler haben so einen Code erzeugt. Heutige auch noch, wenn man erst Registerallokation macht und dann Spillcode erzeugt

### Generatoren

Eingabe = Maschinenbeschreibung + Baumregeln

897 898 $$\Huge(\mathcal{Z})$$

### JIT

902

- AOS Database
    - Dynamische Messwerte des Systems / Schwellwerte für Optimierungen
- noch mehr erklärt, aber ich hab nicht so zugehört (damit endet das Semester so, wie es angefangen hat)

---

---

---

that's it




















---

---

---


# Protokollfragen

- Info aus der letzten Vorlesung:
    - Lexikalische Analyse kommt nicht dran
    - Alles über das geredet wurde kommt dran
        - Syntaxanalyse, DaFluAna kommt eher häufiger dran, AG eher seltener
    - wenn über Z länger geredet wurde kanns schon dran kommen, wenn "nur, damit mans mal gesehen hat" dann nicht


TODO: Transformation, Codegen, Opt Fragen rausschreiben

- Gesamten Stoff können und auch verstehen
- keine leichten Einstiegsfragen
- Meistens erst Intuition, dann Definition erklären
- Nicht von seiner Art einschüchtern lassen

#### Syntaktische Analyse
- Definition LL, SLL, LR, SLR, LALR, closure, GOTO, ACTION, (dynamische) Ankermenge, Rechtskontext, Konflikt, ...
- Beispiel für Grammatik die LR(1) aber nicht LALR(1) ist (und andere Kombinationen)
- Expression-Grammatik linksfaktorisieren
- LR Tabellenkompression (Stichwort Äquivalenzklassenbildung)
- Rekursiver Abstieg anhand Expressions
    - Kontextsensitive Ankermenge (Konkatenation hingeschrieben)
    - Was passiert wenn Fehler bemerkt?
- LALR-Parser anhand $$S \to S(S) \vert \epsilon$$ erklären
    - Wie Fehlerbehandlung, Ankermengen?
        - A: Ankermengen = Rechtskontexte der Nicht-Closure Items. Nicht die in der Closure weil die neue Produktionen anfangen aber man will erstmal die aktuelle Produktion zu Ende bringen.

            Im Panikmodus Tokens überspringen, bis Zeichen aus Ankermenge gefunden. Automatenübergänge in Reduktionszustand, der Ankermenge als Rechtskontext hat

        - Dynamische Ankermengen?
            - A: Abhängig vom aktuellen Kontext = aus welcher Produktion ich komme. Dynamik kommt aus Stack der Funktionsaufrufe
            - Wie implementiert?
                - A: Bei LL bei jeder Parse-Funktion Ankermenge übergeben, bei LR Rechtskontexte der Nicht-Closure-Items des Fehlerzustands
- Wann gibt es ohne Rechtskontexte Konflikte?
    - A: z.B. $$S \to \cdot S(S)$$ und $$S \to \cdot$$, weil Reduktionszustand
- Definition ACTION-Tabelle bei LR
    - Warum $$\operatorname{First}_{k-1}(\gamma \omega)$$ bei Shift-Bedingung notwendig?
        - A: Für Fehlerbehandlung
    - Warum bei GOTO Rechtskontext prüfen?
        - A: Sonst Fehler am falschen Ort erkannt.
    - Merkmal der Fehlererkennung bei LR
        - A: Parserdefinierte Fehlerstelle erkennen. Wenn Rechtskontext nicht beim Shift überprüft, dann evtl. weitere Zeichen eingelesen, obwohl schon an pdef. Fehlerstelle
- Wie von LALR zu LR-Automat?
    - A: Zustände mit gleichem Kern verschmelzen oder gleich LALR-Automat aufstellen
- Wieso LALR-Automat isomorph zu direkt aufgestelltem Automat? [nicht in Vorlesung]
    - A: Weil es ein Faktorautomat ist. [Sowas wie ein Minimalautomat?]

#### Semantische Analyse
- Definition LAG(1), LAG(k), Symboltabelle + Impl (Stack)
- Wofür Operator- und Typanalyse?
    - Wozu Typen es gibt ja auch Skriptsprachen?
        - A: Weniger Laufzeitfehler wegen falschen Typen (z.B. x = int + str)
- Wie Namensanalyse?
    - A: Symboltabelle blabla.. äußerst sichtbare Definition kann man in Symboltabelle hinterlegen als Optimierung. Um das bei Klassen zu optimieren AG benutzen, die Typinfos durchreicht, weil man Typinformationen braucht für richtigen Namensraum.
- Warum Attributgrammatiken selten benutzt?
    - A: zu unflexibel (können AST nicht umbauen, umg-Attribut muss immer übergeben/kopiert werden)
    - Wieso nicht einfach Pointer auf Symboltabelle übergeben?
        - A: "es lag dann doch irgendwie am Overhead fürs Kopieren, auch bei Pointern". Bei modernen Compilern als globale Struktur
- Attributierungsregeln für Expressions aufschreiben
- AST-Aufbau für linksfaktorisierte Expressions
- Wann kann man Grammatik beim Parsen berechnen?
    - A: Wenn LAG(1)
    - Wie beim rekursiven Abstieg implementieren?
        - Ererbte Attribute als Eingabeparameter; synthetisierte Attribute als Rückgabewerte
- OAG von Auswertungsprogramm hinschreiben
- Kann zyklenfreie Grammatik nicht OAG sein? [nicht in Vorlesung]
    - A: Programme sind statische Approximation aller möglichen Abhängigkeiten. Deswegen Abhängigkeiten möglich, die in keinem realen Ableitungsbaum auftauchen würden, aber Erstellung von OAG verhindern.

#### Transformation
- Activation Records Aufbau + Wofür?
- Kurzauswertung
- Sprungtabelle Tableswitch

#### Optimierung
- Definition Daten- + Kontrollabhängigkeit
    - Wie sieht Datenflussanalyse für sichtbare Definitionen aus? Wozu?
        - A: Will für jede Verwendung einer Variable wissen, welche Definitionen dazugehören
- Kontrollflussgraph
- Definition SSA
    - Welche Optimierungen dadurch einfacher?
    - Wo wird Phi-Funktion eingesetzt?
    - Definition Dominanz/-baum/ (iterative) -grenzen
    - Wie abgebaut?
- Slicing

#### Codeerzeugung
- BURS, BUPM
- "Aufgrund vorheriger Protokolle gleich die komplizierteste Regel $$R_i \gets \operatorname{add}(R_i, \operatorname{ind}(\operatorname{add}(C_a, R_j)))$$ hingeschrieben"
- Was bedeuted $$\operatorname{ind}()$$?
- Wann wird welche Regel angewandt?
- Funktioniert auch auf dem AST?
