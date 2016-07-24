---
layout: post
title:  "Sprachtechnologie und Compiler Übung"
date:   2016-04-26 00:00:00 +0100
categories: posts
---

# Übung

# Blatt 1

## 1.1 - Taschenrechner-Lexer

Lexer oft langsamste Phase im Compilerbau (Bsp Compilerpraktikum: Java - Stringkonkatenierung)

Wieso Floats schon im Lexer in `float` umwandeln?

+ \+ Fehler früher erkannt
+ \+ verbraucht weniger Speichert (=> Lösung: Stringtabelle)
- \- Genauigkeit der Datentypen evtl. nicht gleich (zB bei cross-compiling)
- \- im Parser mehr Kontext für Fehler
- Praxis: Alle Tokens als String gespeichert (Libs benutzen, deren Verhalten man konfigurieren kann (z.B. für Floats))

## 1.2 - `grep`-Regex
Erkennt Strings deren Länge nicht prim ist. (Moral: Moderne Regex-Engines sind bis zur Turingvollständigkeit aufgepimpt)

offene Frage: ist `+?` regulär?

- vllt funktioniert ein non-determ. Automaten -> Potenzmengenkonstruktion

## 1.3 - Kontextfreie Grammatik
nicht mehrdeutig =Jedes Terminal in einem anderen Entwicklungsschritt [oder so?]

# Blatt 2

## 1 - EBNF

    <Grammatik> ::= ( <Nichtterminal> ::= <Expr> . \n)*

    <Expr> ::= (<id> |
                    '(' <Expr> ')' |
                    <Expr> <Expr> |
                    (<Expr> '|' <Expr>)
               )
               ['*' | '+']

    <id> ::= <Nichtterminal> | <Terminal>

## 2

## 3
! Man darf nicht mit Sprache $L(G)$ argumentieren (zB jede endliche Sprache wäre regulär und damit $LL(k)$ für ein $k$ aber man kann immer eine mehrdeutige dazu Grammatik bauen, die nicht $$LL(k)$ ist), aber manchmal gute Denkstütze

a) LL(2) und SLL(2)
b) so is das


## 4.2 $|P|$


## 5.1 Parse-Bäume
meh
<!--  -->
## 5.2 Mehrdeutigkeit
Nicht eindeutig, wegen bspw. `Objekt -> Objekt | Objekt Objekt`.

# Blatt 3
Gibt's Tools zum Grammar Engineering? naja! lohnt sich vllt nicht so, aber kp da kennt er sich nicht so aus

Für jede $LL(k)$-Grammatik gibt es auch eine $SLL(K)$-Grammatik

## 1.1
First/Follow berechnen oft in Prüfung

# Blatt 4

## 1 - Grammatik-Klassen
- ✗ LL(0): Konflikt bei $S$
- ✓ LL(1)
- ✗ LR(0): Reduce-Shift-Conflict bei $S \to L \cdot = R$ und $R \to L \cdot$
- ✗ SLR(1): Immernoch Reduce-Shift-Conflict
- ✓ LALR(1): s. Automat
- ✓ LR(1): Weil LALR(1) auch geht


# Blatt 9

## 1 - Ungewöhnliche Fixpunkte

a) von $$ \mathbb{N} \to \mathbb{N} : f(z) = z$$ aber $$ f(\bot) = 0$$

b) man brauch Verband unendlicher Höhe (wg Satz "In einem Verband endlicher Höhe M hat jede monotone Funktion f einen
kleinsten Fixpunkt") Natürliche Zahlen, (+ n 1) als Funktion, unendlich als Supremum, Top "unendlich + 1" als kleinsten Fixpunkt

## 2 - Datenflussanalyse

2.1 Kann man auch mit top initialisieren und sich von oben annähren? schon, aber dann findet man die größten Fixpunkte, nicht die kleinsten. Aber: das Ergebnis ist bei jedem Schritt richtig, wenn man von bot anfängt dann nur am Ende. D.h. man könnte Analyse in der Mitte abbrechen (Aus Gründen der Zeit oder whatev)


# Blatt 13

## Auslagern

einfach nur der Registerdruck oder??
