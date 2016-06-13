---
layout: post
title:  "Requirements Engineering (SS 2016, Jun.-Prof. Koziolek)"
date:   2016-04-26 00:00:00 +0100
categories: posts
---

Zusammenfassung für Req Eng. Work in progress.

# Fundamentals

## Introduction

Why?

- Lower costs
    - error cost
    - rework cost
- Manage risks
    - meet stakeholder's desires and needs
    - reliably estimate deadlines and costs
    - Rule: more RE = less risk (RE effort is inversely proportional to allowed risk)
    - Question to ask:  How much RE do we need to do until the risk is acceptable?
- Keep in mind: economic effects are indirect!
    - RE as such creates costs only

When building such a system...

- How do we determine the requirements?
- How can we analyse and document these requirements?
- How do we make sure that we’ve got the right requirements?
- How do we manage and evolve the requirements?

Boehm's First Law
: Errors are more frequent during requirements and
design activities and are the more
expensive the later they are removed.

!["Fix It Later" is expensive](../../media/fix-it-later.png)



Requirement
: 1. A need perceived by a stakeholder.

    2. A capability or property that a system shall have.

    3. A documented representation of a need, capability or property.

Requirements Specification
: A systematically represented collection of requirements,
typically for a system or component, that satisfies
given criteria.

Lastenheft
: Customer/stakeholder requirements specification

Pflichtenheft
: System/software requirements specification

Requirements Engineering
: A systematic and disciplined approach to the specification and management of requirements with the following goals:

    (1) Knowing the relevant requirements, achieving a consensus among the stakeholders about these requirements, documenting them according to given standards, and managing them systematically. [classic]

    (2) Understanding and documenting the stakeholders’ desires and needs. [customer-oriented]

    (3) Specifying and managing requirements to minimize the risk of delivering a system that does not meet the stakeholders’ desires and needs. [risk-oriented]

<table>
<tr>
<th>approach</th>
<th>definition</th>
<th>metaphor</th>
<th>goal</th>
<th>problems</th>
</tr>
<tr>
<td>classic</td>
<td>The application of a systematic, disciplined, quantifiable approach to the specification and management of requirements; that is the application of engineering to requirements.</td>
<td>upfront engineering</td>
<td>complete, unambiguous requirements prior to design</td>
<td>big and heavy process</td>
</tr>
<tr>
<td>customer-oriented</td>
<td>understanding and documenting the customers’ desires and needs.</td>
<td>customer satisfaction</td>
<td>understand the customer</td>
<td>Why not just code what the customer desires and needs?

Who is "the customer"?</td>
</tr>
<tr>
<td>risk-oriented</td>
<td>Specifying and managing requirements to *minimize the risk* of delivering a system that does not meet the stakeholders’ desires and needs.</td>
<td>balancing effort and value</td>
<td>mitigate risk</td>
<td></td>
</tr>
</table>


## Principles of RE

1. Stakeholders
2. Systems, machines, and context
3. Intertwining of Goals, Requirements, and Design
4. Value-Orientation
5. Validation
6. Evolution
7. Innovation

### Stakeholders

Stakeholder
: A person or organization that has a (direct or indirect) influence on a system’s requirements.

    Indirect influence also includes situations where a person or organization is impacted by the system.

    Ex. Skilift: Betreiber, Skifahrer, Aktionäre ...

Differing viewpoints and needs =>

- Discover conflicts and inconsistencies
- Find consensus
    - Negotiating
    - Moderating
- Determine when *variability* is needed to accommodate different user groups


### Systems, machines, and context
Reqs specify a *system* that is embedded in a *domain context* and may be part of another system. => *multi-level* reqs

Context
: 1. In general: The network of thoughts and meanings needed for understanding phenomena or utterances.

    2. Especially in RE: The part of a system’s environment being relevant for understanding the system and its requirements.

System boundary
: The boundary between a system and its surrounding context.

    Must to be determined by RE.

Context boundary
: Boundary between the context of a system and those parts of the application domain that are irrelevant for the system and its requirements.

![](../../media/re-system-context.svg)

Context models
: - Model system and context (as actors -- admins, users, other systems...)
    - Model actor-system and actor-actor interactions
    - Determine level of specification
    - Do not model system (black box)

![A context diagram](../../media/context-diagram.svg)

#### Mapping world phenomena to machine phenomena

High-level real world reqs have to be mapped to more low-level system reqs (that assume domain properties).

The requirements problem (Jackson)
: Given a machine satisfying the specification $S$ and assuming that the domain properties $D$ hold, the requirements $R$ in the world must be satisfied: $S \wedge D \vdash R$.

### Intertwining of goals, requirements, and design
Waterfall does not work well. Requirements and design can't be seperated.

#### Hierarchical intertwinement

- High-level design decision inform reqs
- Typical layers: business -> system -> software

![](../../media/reqs-design-intertwinement.svg)

##### "What" vs. "How"

- Context-dependent => useless distinction
- Distinguish operationally
    - Statement owned by stakeholder (change requires approval from stakeholder)?
        - => It's a req
    - Statement owned by supplier (supplier may change freely)?
        - => It's a design decision

#### Technical feasibility and innovation

Software architecture *frames*, *constrains*, and *inspires* reqs.

- SA frames:
    - Context and boundaries for reqs
    - Insights into difficulty, risk, cost of implementation
- SA constrains:
    - Technical limitations (obvious)
    - Project limitations
        - too high a cost, risk, time
- SA inspires:
    - Technology creates opportunities (e.g. RFID chips)

#### Validation [position wat]

- Stakeholder may not know what they want at the start
- Get stakeholder feedback on some design decisions
- => Agile


### Value-Orientation
- Customer pay for systems, not reqs
- Good RE creates value (indirectly)
- value = benefit of reducing development risk - cost of specifying req

![Effort vs. value](../../media/re-effort.png)

- weigh stakeholder importance against impact of req
- RE effort shall be inversly proportional to allowed risk

![](../../media/impact-vs-importance.svg)

#### Other risk factors
- Innovative system (distinctiveness)
- Low shared understanding (between devs and stakeholders)
- No reference systems
- Long feedback-cycle
- Bad customer-supplier relationship
- Certification required

### Validation
Every req needs to be validated

![](../../media/re-validation.svg)

### Evolution
Keeping reqs stable <--> permitting reqs to change

- Very short(1-6 weeks) dev cycles
- Explicit req management

### Innovation
- Don't just automate -- strive for making stakeholders *happy*
- *Innovative* reqs are the key

## Classifiying requirements

TODO fix shitty table formatting

<table>
<tr>
<th style="transform: rotate(-90deg)" rowspan="4">Application <-- Order</th>
<th>What is the underlying concern? Was this requirement stated primarily because we need to specify ...</th>
<th>Kind of req</th>
</tr>
<tr>
<td>... some of the system’s behaviour, data, input, or reaction to input stimuli –regardless of the way this is done?</td>
<td>functional requirement</td>
</tr>
<tr>
<td>... a quality that the system or a component shall have?</td>
<td>quality requirement</td>
</tr>
<tr>
<td>... any other restriction about what the system shall do, how it shall do it, or any prescribed solution or solution element?</td>
<td>constraint</td>
</tr>
</table>

![Classification by kind](../../media/req-types.svg)


### Representation

<table>
<th>Form</th>
<th>Definition</th>
<th>Validation levels</th>
<th>Example</th>
<tr>
<td>Operational</td>
<td>Specification of operations or data</td>
<td>Review, test, or formal verification</td>
<td><em>The turnstile control software shall count the number of ‘unlock for a single turn’ commands that it issues to the controlled turnstile.</em></td>
</tr>
<tr>
<td>Quantitative</td>
<td>Specification of measurable properties</td>
<td>Measurement (at least on an ordinal scale)</td>
<td><em>During the operating hours of the chair lift, the system must be available for 99.99% of the time.</em></td>
</tr>
<tr>
<td>Qualitative</td>
<td>Specification of goals</td>
<td>No direct verification. Either by subjective stakeholder judgement of deployed system, by prototype, or indirectly by goal refinement or derived metrics</td>
<td><em>The system shall be highly available.</em></td>
</tr>
<tr>
<td>Declarative</td>
<td>Description of a required feature</td>
<td>Review</td>
<td><em>The system must comply with the privacy law of the country
    where the resort is located.</em></td>
</tr>
</table>

Example:

1. "Any unauthorized access to the customer data shall be prevented"
    - Qualitatively, non-functional
2. "The database shall grant access to the customer data only to those users that have been authorized by their user name and password"
    - operationally, non-functional

### Satisfaction
Hard vs soft reqs

![](../../media/hard-soft-req.svg)

### Kind
    - Function, Data, Behaviour
    - Quality
    - Constraint
### Role
    - Prescriptive
    - Normative
    - Assumptive



## Shared understanding

[NEXT: 2016-05-10-1.mp4]


# RE Practices

## Documenting requirements

## Requirements engineering processes

## Requirements elicitation

## Specifying with natural language

## Model-based requirements specification

## Formal specifications languages

## Validating requirements

## Innovative requirements

## Requirements management


# Enablers and Stumble Blocks

## Requirements tools

## RE under time pressure


# Research Topics

## Architecture-driven RE


# Conclusions

# Protokollfragen

- oberflächliche Fragen

- 3 Definitionen von RE
- 7 Prinzipien des RE aufzählen und kurz beschreiben
    - Was heißt Maschine bei "Systems, machines, and context", Probleme?
        - A: ..., Jacksons Problem, Lücke zwischen Maschine und realer Welt
    - Def Shared Understanding
- UML Diagramme für dynamisches Verhalten nennen und an Beispiel erklären + skizzieren
    - A: State Charts, modellieren externe Events; Sequenzdiagramme, modellieren Nachrichten/Interaktionen
- Zustandsdiagramm "Aufzug" zeichnen
- Klassifizierungen von Anforderungen + Unterpunkte
    - Facettierte Klassifikation
    - Unterschied Qualitätsmerkmal - Funktionale Anforderung
    - Qualitätseigenschaften
- Prozesse des RE, welchen Dimensionen?
- 3 Modellierungsperspektiven
    - Was kann man alles modellieren?
    - Was für Diagramme pro Perspektive?
- Aktivitätsdiagramm zu "Geld vom Bankautomaten abheben"
    - Was heißen einzelne Elemente?
    - Warum Aktivitätsdiagramm bei Funktionsperspektive und Statecharts bei Verhaltensperspektive? ["Preisfrage"]
- Formale Sprache: "Geld abheben" modellieren in Z
- OCL erklären + Beispiel
    - A: Drückt Invarianten, Semantik aus
- Schlussfolgerungen zu den 3 Papers
