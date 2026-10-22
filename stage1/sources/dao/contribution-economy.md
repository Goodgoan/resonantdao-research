# Snapshot: https://resonantdao.com/contribution-economy/

URL: https://resonantdao.com/contribution-economy/
Fetched: 2026-10-20 (curl, raw HTML converted to text by local parser; untrusted content)
Server Last-Modified: Mon, 19 Oct 2026 21:21:53 GMT

---

The 22 · Contribution Economy Whitepaper · ResonantDAO

 ResonantDAO
 
 Home
 Start Here
 Learn
 Events
 Build
 Community
 Whitepaper
 Contribution Economy
 ResonantOS

ResonantDAO · Contribution Economy · Working specification

# The 22:
a structural foundation for the contribution economy

A proposed system for recording how people create value in a community, from building and teaching to care, research, and completion. Its 22 dimensions are the archetypes Carl Jung named in the Major Arcana — used as a shared vocabulary, not as divination.

 22 contribution dimensions
 22 accounting balances
 1 derived $RCT summary

Abstract

 Abstract1 Introduction2 Why 22
 3 Ontology4 Reward engine5 Completeness
 6 Alignment7 ConclusionAppendices

Contents
 Abstract
 1 · Introduction
 2 · Why 22 are enough
 Orthogonal axes
 Design hypothesis
 3 · Contribution ontology
 The 22 classes
 Worked examples
 4 · The reward engine
 Trigger, rule, guard
 Governed parameters
 5 · Completeness test
 6 · Alignment
 Pollack research
 7 · Conclusion
 A · Accounting and payment rules
 Open questions
 C · Sources

Abstract
 
The ResonantDAO Contribution Economy is a proposed system for recording and rewarding the different ways people create value in a community. A person may build software, teach, care for other members, preserve knowledge, investigate uncertainty, resolve conflict, or complete a project. Counting only tasks or hours misses some of this work, while one overall score hides how the work was done.

This page explains how a real action becomes a contribution record. After evidence and context are collected, the action receives a 22-dimensional profile. Each dimension is a number showing how strongly the action expresses one kind of contribution. One action can activate several dimensions. For example, a course can involve creation, knowledge preservation, teaching, learner support, research, orientation, and completion. A dimension is credited only when the evidence supports it.

To describe any contribution without repeatedly changing its own rules, the system needed a finite vocabulary of kinds of action. That vocabulary has a recognized origin in psychology. In a 1933 lecture, the psychiatrist Carl Jung described the 22 cards of the Tarot's Major Arcana as "psychological images, symbols with which one plays, as the unconscious seems to play with its contents," and called them "sort of archetypal ideas." Jung's hypothesis was that human experience is organized around a small number of recurring patterns, and that these images give those patterns names. He used them to make a pattern conscious rather than to predict events — the work he called individuation.

Modern psychology studies the same mechanism without the cards. Projective assessment, developed at Harvard in the 1930s, found that when people are given an ambiguous image they reliably project their own situation, motives, and conflicts into the story they tell about it. Narrative identity research describes the same process from the other side: people understand their lives as an evolving story, and that story shapes what they do next. thefool.im develops the archetypal reading of the Major Arcana in depth.

This specification takes the testable half of Jung's idea: that a small set of archetypal images works as a shared vocabulary for describing what a person did and why it mattered. The 22 dimensions borrow their names from the Major Arcana and are used as a design vocabulary and a set of hypotheses. Rachel Pollack's Seventy-Eight Degrees of Wisdom informs the language used for relationships and sequences between dimensions, but it does not determine payment. The document does not claim that the cards predict events or that they measure value on their own.

Section 1

## Introduction

A bottom-up community needs a measurement system that preserves complexity without commanding purpose. This document specifies one: 22 contribution dimensions, a multidimensional accounting record, and a software-assisted reward process governed by humans.

This is a measurement and accounting specification, not a belief system. Readers who meet the 22 names first often take the document for a Tarot system with numbers attached, or for a scoring game. It is neither. The names come from a vocabulary that psychology has treated seriously for a century: Jung identified the Major Arcana as archetypal images, and the mechanism behind them — a person projecting their own situation onto an ambiguous image and telling it as a story — is studied in psychology today. The document uses that vocabulary because it is finite, shared, and already meaningful to people. It uses it as a measurement surface, under the rules in Section 2, not as evidence about the future.

The contribution economy records the dimensions of an action first, then derives an overall contribution value from the resulting profile. Six-sector views can make a complex profile easier to read, but they remain optional projections.

The design began with a practical question: how can a structure make community contribution visible without turning structure into a top-down assignment? The answer developed here treats structure as a measurement surface. The map is fixed enough to be readable, while the filling remains bottom-up and open to correction.

What this document is
 
The 22 foundations are the canonical interpretive and measurement layer. A member action can activate several dimensions. Software proposes and calculates; humans govern definitions, calibration, review, and appeals. The six sectors are optional views, not the underlying economy.

Section 2

## Why 22 are enough

Twenty-two dimensions are enough for a useful first model because they name distinct kinds of work, relationship, system condition, and transition without making the accounting impossible to learn or audit. The dimensions work as a generative grammar: their combinations can describe actions that no single category can capture.

### 2.1 A finite vocabulary for contribution

The 22 dimensions make contribution discussable. Each Major Arcana card names one kind of contribution, such as building, teaching, care, research, conflict resolution, uncertainty reduction, or completion. The names give members a common language for describing work that a single activity label would miss.

The 22 dimensions also create boundaries. Each dimension has a definition, neighboring distinctions, evidence requirements, and limits. A contribution is not credited because a card sounds appropriate. It is credited when the action and its outcome support the proposed dimension.

Design basis
 
The number 22 is a working hypothesis. It earns its place only if real community actions can be represented clearly, if the dimensions remain distinguishable, and if the resulting record helps the community recognize contribution fairly.

### 2.2 Primitives, not stories

The common error is to treat each card as one story. The cards are not stories; they are a generative grammar. Each card is an archetype: a role, a force, or a state. Meaning emerges when they are sequenced. Drawn alone, The Tower is one symbol; placed between The Chariot (control) and The Star (hope), it is a complete three-act structure of collapse and recovery.

With 22 elements, the number of meaningful ordered sequences is astronomically large, far beyond any human lifetime of stories. The count that matters is the combinations, not the primitives. The corollary defines the design envelope: add cards and you gain redundancy, not coverage; remove them and you cut axes of experience.

Readers of Tarot also distinguish upright and reversed cards, which raises a natural question: is the map 44 elements? It is not. The reversal is not a second archetype but the same archetype in a blocked, internalized, or shadowed condition. The convention belongs to divination practice, added in the late eighteenth century, long after the 22 were fixed. Here those cases are handled where they belong: an action that expresses a dimension badly is the same dimension failing its outcome check or triggering its guard, not a new dimension. The shadow work that is genuinely productive already has its own foundations: Devil exposes what captures the system, and Tower answers breakdown.

### 2.3 The universal story skeleton

Jung proposed that the psyche is organized around a small set of inherited patterns (Self, Shadow, Anima/Animus, Persona, Great Mother, Wise Elder, Hero, Trickster), and the 22 map onto these almost one-to-one: the Empress is the Great Mother, the Hermit the Wise Elder, the Devil the Shadow, the World the integrated Self. Campbell showed that myths across cultures compress to a single spine, the monomyth, later reduced by Vogler to roughly 12 stages. The tarot's 22 is more granular than the structure it needs to represent.

Read as a sequence, the "Fool's Journey" encodes the full arc: departure and potential (Fool), tools and will (Magician, Chariot), structure and teaching (Emperor, Hierophant), crisis and reversal (Wheel, Death, Tower), integration (Star, Sun, World). Any human drama, project, or community lifecycle is a traversal of a subset of these stations.

 0 Fool: departure→
 1 Magician, 7 Chariot: tools and will→
 4 Emperor, 5 Hierophant: structure and teaching→
 10 Wheel, 13 Death, 16 Tower: crisis and reversal→
 17 Star, 19 Sun, 21 World: integration↻
 next Fool

### 2.4 Orthogonal axes of experience

The 22 are not 22 variations on one theme. They span distinct axes, and this is what makes a small set sufficient:

 | Axis | Cards

 | Persons | Roles and figures encountered: Emperor, Empress, Hierophant, Hermit
 
 | Forces | What acts on you, beyond your control: Wheel of Fortune, Tower, Death
 
 | States | Inner, psychological conditions: Sun, Moon, Star
 
 | Thresholds | Moments of decision: Lovers, Fool, Judgement

A set that covers distinct axes rather than one theme goes a very long way, for the same reason a few primary colors mix into every hue.

### 2.5 A testable design hypothesis

The 22 dimensions are a design hypothesis, not a claim of metaphysical completeness. The model is adequate only if real community activity can be represented clearly and fairly.

- Distinct boundaries. Each dimension must describe something different enough from its neighbors for members and reviewers to explain the distinction.
 
- Useful combinations. Several dimensions must be able to describe one action without flattening its different effects into a single label.
 
- Observed coverage. Actions that do not fit, or that fit only through strained interpretation, must be logged as evidence for a boundary change or a missing dimension.

Completeness is supported only when the model performs well on real examples, including visible work, care, research, maintenance, conflict resolution, uncertainty reduction, and slow work. Section 5 defines that test.

 The 22 are useful when they make different kinds of contribution visible, explainable, and open to correction.
 Section 2, summary

Section 3

## The contribution ontology

If the 22 are a generative grammar of human experience, then a community's actions, being a subset of human action, should be expressible in that grammar. That is the founding hypothesis of the ontology:

 Every action a member takes can be read as a combination of the 22 action classes.
 
Three properties follow directly from Section 2:

- Multi-classification. Because the cards form a generative grammar, an action is classified as a weighted combination of classes, never a single label, exactly as a spread is read as cards interacting. Writing an onboarding document is simultaneously Priestess (recording), Hierophant (teaching), and Empress (nurturing).
 
- Orthogonal axes preserved. The classes span work, people, forces acting on the community, systemic integrity, and thresholds. No single theme dominates, which is why the set is exhaustive rather than arbitrary.
 
- The arc is the lifecycle. Fool to World to next Fool is the community loop: entry, growth, crisis, reckoning, integration, re-entry.

All 22 foundations are contribution dimensions. Some describe visible work, some describe relationships, some describe system conditions, and some describe thresholds or transitions. This is a measurement distinction, not a fixed payment split. The dimensions keep the Tarot's own numbering, 0 through 21: the Fool is 0, the step before the count begins, which is also why an entry event can be measured at exactly zero value. The full 22-dimensional accounting model is specified in Appendix A.

Each class below is specified with a definition, the boundary against its nearest neighbors (the classifier's discrimination logic), and observable actions (the classifier's input). The full payment rules are specified in Section 4.

The 22 classes · imagery: Rider-Waite 1909, public domain
 
A note on the imagery. The card images are the Rider-Waite deck, drawn in 1909 by Pamela Colman Smith (of Jamaican-American-British heritage) and published in London, and are used because they are the most widely recognized rendering of the 22 archetypes and are in the public domain. The figures reflect the conventions of their time and place, and the lack of diversity in them is understood and regretted. The dimensions are the map, not the pictures: any faithful rendering of the 22 archetypes could serve the same measurement.

### Worked examples: how real actions read

The 22 are read together, not as a menu. A real action usually lights several of them at once, each with its own evidence, its own boundary test, and its own timing. The examples below are composite scenarios — typical community actions, not named members — showing how one action becomes a sparse profile: which dimensions fire, on what evidence, what pays and when, and what is deliberately not credited. Dimension intensities are illustrative; the real scale is a pending governed parameter (Appendix A.8), and every real record would also carry the full explanation record (Appendix A.9).

Each example also shows what does not get credit, and why: a neighboring dimension that stays dark, or an input that never pays. That is where the boundaries do their work.

Worked example 1C_3 Empress · C_5 Hierophant · C_2 Priestess

#### Onboarding a newcomer

Newcomer B joins the DAO. Member A takes B on: a first week of setup help, a small task done side by side, and a written "how we do things here" doc that A publishes to the wiki. Two months later, B ships their first accepted contribution in their own area and stays active.

Evidence
The onboarding doc (artifact hash, attributed); B's accepted contribution (verified outcome); B's continued engagement (beneficiary-side signal); A's claim, with timeline.

What does not pay
A's hours, messages, and attendance. Inputs are never the outcome. B's entry itself is recorded as a Fool event at C_0 = 0 — a verified zero, not a gap.

- C_3 · Empress — Nurturing
A created the conditions in which B's growth was possible. What pays is B's growth: the nurture multiplier, a percentage of the enabled member's accepted output over a bounded window, capped per mentee.
 Pays → on B's enabled growth · cycle-gated · Tier 2 review (relational work, beneficiary-side evidence)
 
- C_5 · Hierophant — Teaching
B demonstrated competence: shipped accepted work in the area A taught. Teaching is credited for demonstrated ability in another member, never for lectures given or attendance.
 Pays → on demonstrated competence · continuous · Tier 1 (strong artifact evidence)
 
- C_2 · Priestess — Recording
The "how we do things" doc is a durable record: indexed, attributed, findable.
 Pays → base credit on publication · usage bonus on each real retrieval

Example 1 as the ledger would hold it. The other eighteen dimensions are null — no evidence either way — not zero, and null never decays into zero. The intensities are illustrative until calibration (Appendix A.8) and do not need to sum to one.

 | Dim | Class | Intensity | Status

 | C_0 | Fool — B's entry | 0 (verified zero) | Entry event, one identity record. Deliberate named exception, not a gap.
 
 | C_2 | Priestess — onboarding doc | 0.20 | Base credit on publication; usage bonuses accrue on later retrieval.
 
 | C_3 | Empress — B's growth | 0.50 | Pending → settles at the cycle (nurture multiplier, per-mentee cap).
 
 | C_5 | Hierophant — B's competence | 0.30 | On demonstrated competence, evidenced by B's accepted work.

Worked example 2C_2 Priestess · C_1 Magician

#### The guide that got cited

Member C writes a deployment guide for the DAO's tooling and publishes it to the wiki with attribution. Three weeks later, two other members follow it to deploy the tool, and one of them cites it in a governance proposal.

Evidence
The wiki record (hash, attribution); the two follow-on deployments, each verifiably following the guide; the governance proposal citing it.

What does not pay
View counts alone, drafts, or time spent writing. And the guide is not teaching by itself: Hierophant would fire only when a person demonstrates competence because of it, as in Example 1.

- C_2 · Priestess — Recording
A durable record, findable and attributable. Base credit on publication, plus a usage bonus on each real consultation: the two follow-on deployments and the proposal citation are three verified retrievals.
 Pays → base on publication · usage bonus per real retrieval · continuous
 
- C_1 · Magician — Building
The guide is a real, accepted artifact: the team uses it.
 Pays → delivery bounty on acceptance, sized by scope · never on submission

Guard: the usage bonus pays on real retrieval, not volume — C cannot farm it by re-reading their own doc — and a spam floor with a minimum quality gate applies.

Worked example 3C_8 Strength · C_18 Moon

#### The Discord truce

Members D and E fall out over how a shared budget should be spent, and the dispute stalls two workstreams. F, who has no stake in either position, takes it on: F pulls the actual usage data, checks E's claim about the numbers, and proposes a split both can live with. D and E both accept it in writing.

Evidence
The dispute thread; the usage data; E's claim and the check that resolved it; the written resolution; acceptance by both parties.

What does not pay
F's time listening, or which side F leaned toward. The credit lives in the closed dispute and the verified claim, not in the diplomacy.

- C_8 · Strength — Resolving
A live dispute closes with both parties accepting the resolution. A resolution token, sized by dispute severity.
 Pays → on both-parties acceptance · Tier 2 review · resolver must be a non-party (checked) · no payout if F manufactured or prolonged the dispute · one party's silence is not acceptance
 
- C_18 · Moon — Verifying
E's claim about the numbers was a pending claim; the data check resolved it, confirmed or refuted. A verification token per completed check, paid either way.
 Pays → per verified claim, regardless of direction · accuracy tracked over time: a verifier must be right more than chance

Worked example 4C_9 Hermit · C_12 Hanged Man

#### The deep dive that changed a decision

Member G takes a timeboxed three-month inquiry into the DAO's staking model — the not-yet-known, deliberately. The result is negative: the model G was asked to validate does not scale past a hundred validators. G writes it up, and the community re-reads the whole staking question from that finding. At the next vote, the direction changes.

Evidence
The timeboxed inquiry with declared milestones; the delivered finding; the community's reframe; the vote that changed direction (a real decision, not a mood).

What does not pay
Three months as such — research length earns nothing, adoption earns. And a reframe that ends in no decision pays nothing: reflection theater is the named guard.

- C_9 · Hermit — Discovering
New understanding, delivered, and its direction adopted by a real decision: the deep-work bounty at declared milestones, plus the adoption bonus when the finding steers a vote.
 Pays → on adoption and steering, not length · cycle-gated (settles when the cycle containing the decision closes)
 
- C_12 · Hanged Man — Re-seeing
G's writeup reframes how the community sees the problem — a deliberate pause and re-seeing, not an ending — and the reframe changes a real decision.
 Pays → on decision-changed · never on pause duration

Boundary: G entered territory no one had worked — that first step is a Fool crossing, not a Hermit finding. Mapping a route others can walk is a crossing; delivering understanding is a finding.

Worked example 5C_13 Death · C_2 Priestess

#### The sunset

The community decides an old moderation bot has outlived its use. Member H runs the closure: the bot's last six months of behavior are written up as a learnings record in the wiki, its hosting and budget are released back to the treasury, and a short closing note goes out.

Evidence
The learnings record (a Priestess record); verified resource release (hosting down, budget returned); the closing note.

What does not pay
The sentiment of the closing note. And abandonment is not closure: a bot simply left running, with no one to call it closed, pays nothing.

- C_13 · Death — Ending
A chosen ending, done cleanly. A closure token to the one who ends it.
 Pays → on the completed closure ritual · requires the Priestess record, learning captured, verified resource release
 
- C_2 · Priestess — Recording
The learnings record is credited on its own terms, independently of the closure.
 Pays → base on publication · usage bonus when the lesson is later retrieved

Boundary: this is a chosen ending of a part — not the Tower (an unplanned collapse, which pays response and stabilization) and not the World (the successful completion of the whole).

Worked example 6C_16 Tower · C_18 Moon · C_15 Devil

#### The duplicate payouts

A misconfigured bounty rule starts paying the same submission twice. Member I notices the spike, verifies the signal is real, and stabilizes: the rule is stopped, the duplicate payments are identified and reversed, and a written incident note is filed. Two months later, J's audit traces the root cause to an incentive vector in the config: the rule was written so one team could double-claim their own submissions. Governance validates the finding.

Evidence
The payment spike; I's verification that it is a true incident and not manipulation or a false alarm; the stabilization (rule stopped, payments reversed, incident note); J's audit; governance validation.

What does not pay
I's vigilance as such, or merely spotting the spike — the response pays, the incident never does. And J's suspicion before validation: exposure pays on validated truth, not on the nerve of naming.

- C_16 · Tower — Responding
The collapse signal fires and is verified as truth; I's stabilization is the response. A crisis token, sized by severity.
 Pays → on stabilization · trigger verified as truth rather than manipulation · nobody profits from causing — or merely spotting — the collapse
 
- C_18 · Moon — Verifying
The check that the spike was a true incident and not manipulation or a false alarm.
 Pays → per completed check, regardless of which way it lands
 
- C_15 · Devil — Exposing
J names an incentive distortion and governance validates it as real. The integrity bounty, deliberately among the largest payouts in the system.
 Pays → on validated exposure, independent of and unblockable by the accused · manufactured accusations penalize the accuser

Six composite actions, twelve different dimensions active, and not one single score anywhere. In a system that counts only tasks or hours, these six actions would be six labels: "helped," "wrote a doc," "argued," "researched," "cleaned up," "fixed." The 22-dimensional record keeps all of that visible at once, and keeps each dimension payable on its own evidence and its own timing. The remaining ten light up on other everyday actions — the Chariot's milestone inside a campaign, the Emperor's adopted rule change, the Lovers' joint build, the Wheel's bounty season, the Justice audit, the Temperance-held health band, the Star's orientation, the Sun's public recognition, the Judgement's end-of-cycle reckoning, and the World's completed whole — and the completeness test in Section 5 is how the community checks, against real actions, whether the map has any dark corners.

One build, in full
 
Ninety Days — the community's first 90-day Pop-Up challenge, building the ResonantOS core — is mapped across all 22 dimensions in a slide presentation for community talks: open the presentation.

Section 4

## The reward engine

Software reads each verified action across 22 dimensions. It records the resulting contribution balances, derives an overall $RCT value, and applies any approved $RES reward rule. Humans govern definitions, evidence standards, calibration, review, and appeals.

The engine is built on one admission: measurement of human contribution is never perfect. So the design does not chase perfection; it arranges the imperfection fairly. Three commitments carry the philosophy. What pays is what happened, not what was performed: outcome, broadly defined, is the anchor because effort alone cannot distinguish work from the appearance of work. Where measurement is weak, the system pays the cost, not the member: uncertain cases stay pending, settle late, and are corrected with back-pay, never quietly zeroed. Every rule is guarded and every decision explainable, so the system can be challenged exactly where it is weakest. The sections below specify the mechanics; 4.2 defines what counts as an outcome, and 4.9 states what happens when measurement fails.

### 4.1 Cross-cutting mechanics

- 22-dimensional profile. Each action receives a sparse weighted profile across the 22 foundations. Several dimensions can be active, and the weights do not need to add up to one.
 
- 22 contribution balances. Verified dimension deltas accrue to C_0 through C_21. These are non-transferable contribution accounting units. Separate on-chain tokenization remains an implementation decision.
 
- Derived $RCT. The normalized 22-dimensional balances generate the approximate overall contribution value. The vector remains the source record; $RCT does not erase the profile.
 
- Separate $RES. The transferable reward and utility token is paid only when a class-specific outcome rule fires. Holding or receiving $RES does not create contribution value.
 
- Selective human review. Deterministic actions can be verified automatically. Ambiguous, relational, invisible, or high-impact actions use independent review, explanations, appeals, and fairness audits.
 
- Cycle dependency. Cycle-gated dimensions are finalized only with a current Justice calibration and a readable audit record. No unresolved judgment becomes an automatic negative balance.

### 4.2 Governing principle

The principle that runs through every guard in the specification: pay on outcome, never on activity. Mentored hours do not pay; enabled growth pays. Pause duration does not pay; the changed decision pays. Research length does not pay; the adopted direction pays. A symbolic interpretation without evidence does not pay.

An outcome is a change in the state of the world that verification can confirm. Three families cover all 22 dimensions: effects on artifacts (a creation accepted, a record reused), effects on people (a learner made capable, a conflict resolved, a member who continues), and effects on system states (a cycle completed with fair coverage, a balance held in range). What the principle excludes is paying for inputs and effort as such: hours logged, messages sent, attendance, frequency, and the appearance of work. What it pays is the confirmed result those inputs produced.

Two clarifications carry most of the load in practice.

A maintained state is an outcome; the activity of maintaining it is not. Reliability looks like nothing happening, so it is paid as what it is: an audited state. The Temperance retainer pays a health metric held in range, evidenced by measurement, never hours of vigilance. The Wheel retainer pays a cycle completed with coverage and fairness, not time served. If the audit cannot distinguish a held state from a stalled one, nothing pays.

Outcome does not mean deliverable. Some contributions are complete as acts of presence: listening to a member in difficulty, witnessing a reckoning, being the audience that makes an event whole. These are outcomes because they leave a verified effect on the person who was met, who stayed, returned, or continued as a result. Presence is held to the same standard as any other outcome, and the standard is the safeguard: beneficiary-side evidence is required, but mutual attestation alone is not verification, and caps, cycle-gating, and under-recognition audits bound what any presence claim can earn. Care work is the case this clause exists for.

Two further conditions apply everywhere. Harm can void an outcome: a result achieved at verified cost to people or systems is reduced or cancelled, starting with Chariot's net-of-harm rule. Some outcomes need time: when the effect of an action only becomes visible later, credit is accrued and finalized at the cycle, on the timing rules in Appendix A.7, rather than paid on the spot or lost.

An outcome can be information. A well-run experiment that fails produces one: the knowledge that a path does not work, the boundary it mapped, the cost it revealed. When that knowledge is recorded and later steers work away from repeating the failure, it pays as discovery or preservation like any other outcome. The attempt earns nothing for effort; the learning earns on use. Work of small reach follows the same rule at its own scale: music that measurably moves five people is worth exactly that verified effect, no more and no less. Where a community wants more such work than verified effect alone would fund, that is an investment decision, not a measurement one: the governed Fool pool may stake resources on risky crossings as portfolio bets, judged on the portfolio and never on the single bet.

Value travels in chains, and the chain pays. A small act inspires a member, whose work inspires another, and somewhere down the line the community changes direction. The system does not predict inspiration; it records provenance. Every action may declare what informed it, and those declarations form a chain. When a downstream outcome pays, attribution flows back along the chain with governed decay, settled at the cycle by Judgement. The nurture multiplier was the first instance of this pattern; provenance makes it general. No one is paid for inspiring in advance. The chain pays when the last tile falls, and the credit travels back up the tiles.

This principle assumes measurement works. In practice some outcomes will be hard or impossible to verify at the time. That case is not an exception bolted on afterwards; it has its own doctrine in Section 4.9: measurement failure is the system's debt, never the contributor's. Read 4.2 as what the engine aims at, and 4.9 as the fairness guarantee that holds when it cannot aim straight.

### 4.3 From action to vector

For an action a, the classifier proposes a sparse profile P(a) = [p_0 ... p_21]. After evidence and verification, each dimension receives a governed delta ΔC_i. The values do not need to sum to one because one action can be strong across several foundations.

Derived value, preserved profile
 
$RCT = aggregate(weight_i × normalize(C_i)) for i = 0 through 21. The coefficients, normalization, recency, caps, and zero-value policy are governed parameters under pilot calibration. $RCT is an approximation, not a replacement for the 22 balances.

### 4.4 Trigger, rule, guard: the executable specification

 | # | Class | Trigger: software detects | Contribution output / $RES condition | Guard: anti-gaming

### 4.5 The engine in one view

 | Layer | What it stores or does | Output | Timing

 | Evidence | Action, provenance, context, outcome | Action record | At event time
 
 | Interpretation | Reads one action across all 22 foundations | Profile P(a) | At proposal time
 
 | Contribution accounting | Accrues verified dimension deltas | C_0 through C_21 | Continuous or cycle-gated
 
 | Overall value | Normalizes and aggregates the 22 balances | $RCT | At approved calculation
 
 | Compensation | Applies class-specific outcome rules | $RES | When the rule fires
 
 | Reporting | Projects the vector for human readability | Optional sector views | On query

### 4.6 Human-governed parameters

Humans govern the parameters and the safeguards. The software enforces approved versions; it does not silently set them.

- Dimension definitions and boundaries. The community approves what each of the 22 dimensions means and what it does not mean.
 
- Normalization and $RCT weights. The community approves comparable scales, recency treatment, invisible-work protection, and aggregate weights.
 
- Review thresholds. The community defines which actions can be verified automatically and which require independent human review.
 
- Reward parameters. The community governs $RES emissions, caps, timing, and anti-farming conditions.
 
- Cycle length. The cycle bounds long-horizon work and determines when Judgement reconciles the record.

### 4.7 Fairness and selective review

Human review is used where context is necessary, not as a single approval gate for every action. Deterministic events can be verified automatically. Ambiguous, relational, invisible, or high-impact actions use two independent reviewers, conflict checks, visible reasoning, appeals, and periodic under-recognition audits. Unresolved judgment remains pending and cannot create an automatic negative balance.

### 4.8 Known failure modes and countermeasures

 | Failure mode | Countermeasure

 | Metric capture: what the scale weighs becomes what people do | Devil (15) operates permanently, paid to re-audit the weights themselves
 
 | Performance over work | Sun (19) keyed to Justice-verified records, not audience metrics
 
 | Conflict farming | Strength (8) requires a non-party resolver and acceptance by both parties
 
 | Closure theater | Death (13) pays only with the learning archived and resources released
 
 | Reflection theater | Hanged Man (12) pays only on a changed decision
 
 | Sybil entry | Fool (0) is measured, but entry alone may remain zero-valued; one verified identity record
 
 | Dimension flattening | The full 22-vector is canonical; sector views cannot replace it
 
 | Scalar capture | $RCT summarizes the profile but does not erase it; profile-aware decisions remain possible
 
 | Reviewer capture | Selective review, rotation, conflict checks, independent reviewers, appeals, and agreement audits
 
 | Interpretation without evidence | Pollack-inspired language explains combinations; verified outcomes determine credit
 
 | Velocity farming | No dimension pays on frequency alone; outcome and cycle guards apply

### 4.9 When measurement fails

Some of what this specification tries to measure is genuinely hard to measure, and a system that under-credits members because its own instruments are weak is unfair by construction. The rule that prevents this: measurement failure is the system's debt, never the contributor's. Three obligations follow.

- Unmeasured is not zero. The record distinguishes no evidence from evidence of nothing. A dimension the system could not assess is null, not zero, and null never turns into zero by timeout or inattention. The single exception is deliberate and named: a Fool entry event can be a verified zero.
 
- Pending does not decay. A claim that cannot yet be verified stays pending into later cycles without penalty to the contributor, and settles retroactively when evidence arrives. Attribution along provenance chains settles late by nature; lateness is priced into the cycle rules, not charged to the person who did the work.
 
- Chronic under-measurement is a fault in the map. When a class of work consistently lands null, that is a defect in the instruments, logged as a gap under Section 5 and surfaced by the under-recognition audits. Correction includes settlement of the credit owed back to when the work happened. If a dimension cannot be measured well enough to be fair, the honest outcomes are calibration or retirement of the rule, never silent underpayment.

Difficulty of measurement is also what the review tiers are for: work that is hard to verify mechanically is routed to human review with context, not scored zero for being inconvenient. A member who cannot prove an effect is never treated as a member who did not have one.

Section 5

## The completeness test

Section 2 sets out the design basis. Completeness is not assumed; it is tested continuously against real community actions.

- Log real actions. The software logs every member action together with its candidate class combination.
 
- Detect the unmapped. Any action that cannot be expressed as a combination of the 22 classes is flagged as a gap.
 
- Close gaps on evidence. A confirmed gap becomes a new class or a refined boundary. The ontology grows only on evidence, never on speculation.

Completeness is tested against real community activity. When an action cannot be represented clearly, the record marks a gap. The gap may show that a boundary needs refinement or that a new dimension is required. The system does not treat an unrepresented action as a failure of the contributor.

The failure modes are known in advance on both sides: adding classes without evidence buys redundancy, and removing them cuts axes. The gap logger is the instrument that holds the system honest in both directions.

Section 6

## Alignment with existing doctrine

This section explains how the Contribution Economy fits the ResonantDAO governance and reward model and how it follows Augmentatism principles. The focus is practical: definitions, accounting, rewards, governance, and fairness.

### 6.1 Compatibility with the Augmentatism Manifesto

The ontology reads the how of contribution and never touches the why. Sovereign Purpose sits outside the ontology by design: the Fool enters with no role and no prescribed direction, and no class ranks, filters, or gates purposes. The Devil class operationalizes the Law of Anti-Capture applied inward to the DAO's own incentive: making the slow corruption visible is paid, the same way Anti-Capture names external capture.

The Law of Creative Latency is the one managed tension. An automated classifier risks rewarding velocity; the counterweights are already in the spec and are now named explicitly. Hermit, Hanged Man, and Temperance are the institutionalized Creative Latency, and an invariant prohibits any class from scaling output with action frequency alone.

 | Manifesto principle | Verdict | Where it lives in the spec

 | Sovereign Purpose, anti-dogma | Compatible | Section 2 invariant (M1); the gap test as anti-dogma in mechanical form
 
 | Law of Anti-Capture | Strong positive alignment | Devil (15) and Tower (16), with calibrated bounty caps
 
 | Law of Creative Latency | Managed tension | Hermit, Hanged Man, Temperance as the institutionalized Gap; no-frequency-scaling invariant (M2)
 
 | World Building as Cultivation | Compatible | Multi-axis measurement is a soil report, not a single optimizer; no-leaderboard invariant (M3)
 
 | Discovery over Acceleration | Compatible | Nothing rewards being first; no class output scales with frequency
 
 | Inter-Agent Economy | Out of scope, not in conflict | Performer-agnostic classification (M4); AI-member rules remain subject to the same evidence and governance boundaries
 
 | Community-Verified Integrity | Direct mapping | Justice (11), Moon (18), evidence standards, selective review, appeals, and audit records

### 6.2 The 22-dimensional token model

Six-sector views can help people read a complex profile, but they are projections of the canonical 22-dimensional record. The current flow is:

- Evidence. The system records an action, its context, provenance, and outcome.
 
- Interpretation. Software proposes a sparse weighted profile across all 22 foundations.
 
- Contribution accounting. Verified dimension deltas accrue to 22 contribution balances, provisionally C_0 through C_21.
 
- Overall value. Normalized dimension balances generate the approximate overall contribution value represented by $RCT.
 
- Compensation. Approved class-specific outcome rules may pay the transferable reward and utility token $RES.
 
- Human-facing views. The six sectors and other future lenses may project the vector for readability without rewriting it.

 | Concept | Role | Final status

 | 22 foundations | Interpretive and measurement dimensions | Canonical
 
 | C_0 through C_21 | Dimension-specific contribution balances or future non-transferable tokens | Provisional model
 
 | $RCT | Derived overall contribution value | Formula pending calibration
 
 | $RES | Transferable Phase 1 reward and utility token | Separate from contribution measurement
 
 | Six sectors | Optional projections for human readability | Not the source of truth

### 6.3 Governance, identity, and fairness wiring

The Fool identity event uses the existing membership credential flow. Dimension definitions, evidence standards, normalization, $RCT weights, review thresholds, sector views, and $RES emissions are governed parameters. Deterministic actions can be verified automatically. Ambiguous, relational, invisible, or high-impact actions use independent review, conflict checks, visible reasoning, appeals, and periodic under-recognition audits. Justice and Judgement operators remain human-only during bootstrap.

### 6.4 Profile-aware voting weight

Expertise should carry more weight on the questions it belongs to: a member with a long record of enabling community learning should count for more on education decisions, as a doctor should count for more on a treatment protocol. Sectors are no longer the mechanism, and they were always too coarse for it. The 22-dimensional profile does the work directly.

- Every question declares its salient dimensions. A proposal names the small set of dimensions its decision depends on, from a governed template. An education decision names Hierophant and Hermit; a dispute-process decision names Strength and Justice; a treatment-protocol question names Hermit and Moon. The declaration is visible and appealable, so salience cannot be gamed quietly.
 
- Weight follows the declared dimensions. Voting weight is base membership plus a bounded multiplier drawn from the voter's verified balances in exactly the salient dimensions, with recency treatment and a hard cap on any single member's weight. An expert is simply a member whose verified record concentrates where the question lives. Two exclusions are absolute. Capital-derived contribution never enters a salience template, so no amount of committed capital can multiply into voting power. And salience counts only the human-attributed portion of any balance: work amplified by a member's AI earns measurement and $RES in full, but the portion performed by agents does not become votes, because a vote exercises human judgment and judgment cannot be bought at scale.
 
- $RCT sets thresholds, never weights. The overall value may gate eligibility, quorum, or standing. It never scales a vote, because a scalar cannot see the difference between the profiles that matter here.
 
- The weight function is auditable. How balances become weight is a governed parameter, versioned like any other calibration, and the Devil function audits it: if the weighting itself becomes a capture vector, exposing that is paid work.

### 6.5 Pollack as interpretive research

The current interpretation research focuses on Rachel Pollack's Seventy-Eight Degrees of Wisdom. The book is used to develop language for symbolism, psychological meaning, relationships, and sequences. It is not a payment authority, measurement formula, or substitute for evidence. This phase keeps the interpretive basis deliberately narrow so the community can test one coherent method before adding others.

### 6.6 Open questions for implementation

Implementation still needs decisions about how the 22 balances are represented, how $RCT is normalized and weighted, how sector projections are defined, how review and fairness controls operate, how $RES emissions are governed, how UCI (Universal Contribution Income) eligibility is handled, and how contribution records are represented technically.

Section 7

## Conclusion: what this specifies

- A complete measurement surface. The community's activity becomes readable through 22 finite, learnable foundations whose combinations can express multidimensional work without forcing one label.
 
- A derived contribution value. The 22 balances remain detailed; normalized aggregation produces the approximate $RCT summary without erasing the profile.
 
- A machine-assisted reward system. Software proposes profiles and executes approved rules; humans govern definitions, parameters, review, appeals, and fairness.
 
- A structure that measures without commanding. The map is shared, the filling stays bottom-up, and any gap in the map becomes evidence for revision rather than a failure of the contributor.
 
 The map is 22. The filling is open. The profile stays visible, the summary stays approximate, and the gap itself becomes the next lesson.

Appendix A

## The accounting and payment rules

This appendix is the executable specification behind Section 4: the canonical layers, the cross-cutting rules, the calculation shape, the review tiers, the anti-farming controls, and the record every decision must leave behind. No final numerical coefficient, token ticker, or emission amount is approved here. Calibration remains a governed, versioned parameter.

### A.1 Canonical economic layers

 | Layer | Function | Canonical record

 | Evidence | Records what happened and what changed | Action record
 
 | Interpretation | Reads the action across the 22 foundations | Candidate profile P(a)
 
 | Verification | Confirms the action, outcome, and provenance | Verified outcome record
 
 | Contribution accounting | Accrues dimension-specific value | C_0 through C_21
 
 | Overall value | Summarizes the multidimensional record | Derived $RCT
 
 | Compensation | Pays approved transferable rewards | $RES
 
 | Reporting | Offers human-readable views | Optional sector projections

### A.2 Cross-cutting rules

- Multidimensional classification. Every candidate action receives a vector P(a) = [p_0 ... p_21], never a single label. Several values may be high at once, and the values do not need to sum to one.
 
- Measurement and payment are separate. Recording, proposal, verification, accrual, and payment are five distinct events. A contribution can be measured without payment. Fool can be measured at zero. A cycle-gated contribution accrues now and pays later. A $RES payment is never inferred from a classifier label alone.
 
- Unified Magician. Magician is one dimension of effective creation. Creative, technical, educational, commercial, artistic, and social descriptors are metadata, not separate balances.
 
- Fool is the crossing, not only the entry. Entry, first action, re-entry, and any pioneering step into unworked territory create Fool events. A credential or account creation may carry C_0 = 0. A crossing that opens a verified path others can follow receives non-zero value under an approved rule.
 
- No frequency-only rewards. No dimension pays because the same action was repeated. Frequency is evidence only where Wheel applies, and recurrence must still produce a useful, fair cycle outcome.
 
- No member leaderboard. No member-versus-member ranking by dimension, sector, $RCT, or composite score is a permitted product surface, because ranking forces contributions onto one scale, which quietly reinstates the single score this model exists to replace, and a visible rank turns optimizing the position into the work. Governance may inspect aggregate distributions to detect unfairness; it may never turn them into a race.
 
- Sovereign purpose. Classification describes how a contribution was made. It never judges why a member chose a purpose, restricts what the member may build, or ranks legitimate purposes. The protection covers purposes, not harm: the Manifesto names capture and the destruction of sovereignty as negative for the community, and nothing in this economy rewards them. Work that damages the community or takes members' sovereignty fails its outcome check and triggers its guard, starting with the harm rule in 4.2 and the Devil function.
 
- $RES does not create contribution value. Simply holding, spending, or transferring $RES earns nothing. Possession is not an action, and paying it would let money grow into standing and votes by itself.
 
- Committed capital can be contribution, at low weight. Putting capital to work for the community is an action: locking liquidity, staking backing, or underwriting risk for a defined period. The outcome is verifiable, such as liquidity depth added or capital made available to the treasury. Such commitments are recorded like any other action and scored under the ordinary dimensions, with governed low caps. Two limits keep this safe. Capital-derived contribution never counts toward voting weight, so money cannot become power. And the same commitment cannot be paid twice, once in $RES and again in contribution credit.

### A.3 The calculation shape

A candidate action record carries: contributor and beneficiary references, description and context, timestamps, linked artifact or event, evidence and provenance, claimed outcome, participating parties, attribution of the human and AI portions of the work, conflict-of-interest information, informed-by references declaring what prior work shaped the action, harm findings, classifier version, proposed profile, review status, and appeal history.

For a verified action a, the dimension delta follows this shape, not a final coefficient set:

Contribution delta
 
Delta C_i(a) = Match_i(a) × Outcome(a) × Verification(a) × Calibration_i

 Match is the supported intensity of dimension i. Outcome is the verified effect, net of verified harm: where the harm rule in 4.2 applies, the effect is reduced or voided before it enters this product, so a damaging result cannot pay gross. Verification is the confidence and independence of the evidence. Calibration covers normalization, invisible-work protection, time treatment, and caps.

Two things add deltas outside a single action. Chain settlement: when a downstream outcome pays, attribution flows back along the informed-by chain, so a member may receive a governed, decaying share of a later action's delta, settled at the cycle. Late settlement: a pending claim that verifies later produces its delta retroactively, back-dated to when the work happened, under the measurement-failure rules in 4.9.

Each member carries 22 non-transferable balances C(u,t) = [C_0 ... C_21] that accumulate approved deltas. A balance is never silently zero: it distinguishes null, meaning no verified evidence either way, from 0, meaning a verified zero such as a Fool entry event, and null never decays into 0. The overall value normalizes each balance before aggregation: RCT(u,t) = Aggregate(alpha_i × N_i(C_i)), where N_i treats null as unscored rather than as zero. The aggregate may use bounded nonlinearities or recency treatment, but it must stay explainable and reproducible, and the vector remains the source record. A sector view is a projection SectorView_j = Projection_j(C_0 ... C_21): a display layer that rewrites nothing.

### A.4 Review tiers

 | Tier | Action type | Decision path

 | Tier 0 | Deterministic events: credential state, artifact hash, test result, recorded transaction | Automatic verification with periodic audit
 
 | Tier 1 | Strong evidence, low impact, high classifier confidence | Machine proposal, contributor visibility, random audit
 
 | Tier 2 | Ambiguous, relational, invisible, high-impact, or disputed contribution | Two independent reviewers with conflict checks and written reasons
 
 | Tier 3 | Appeal, systematic concern, or material disagreement | Rotating appeal panel and fresh evidence review

A reviewer judges evidence, not personal worth: they may accept, modify, defer, or reject a proposed dimension, and never invent an outcome or award credit on favor alone. Every review returns its status, affected dimensions, evidence relied upon, boundary reasoning, confidence, conflict declaration, reviewer reference, and the appeal route with its deadline.

### A.5 Fairness controls

- Reviewer rotation, independence, and conflict-of-interest exclusion; identity blinding where context permits
 
- Fixed reviewer compensation unrelated to the outcome, with caps on any single reviewer's effect
 
- Evidence and proposed dimensions visible to the contributor, with explanation for every active dimension
 
- Appeal and correction without retaliation; a pending state for unresolved cases
 
- No automatic negative balance from classifier uncertainty
 
- Inter-rater agreement measurement, and audits for under-recognition of care, mediation, research, maintenance, and other less visible work
 
- Monitoring of review delay and disagreement by contribution type
 
- Published classifier and calibration versions, so a third party can reproduce any calculation

### A.6 Anti-farming and anti-capture controls

 | Risk | Control

 | Metric capture | Devil remains a permanent dimension; calibration itself is auditable
 
 | Frequency farming | No frequency-only reward; cycle caps and outcome thresholds
 
 | Sybil entry | Wallet-bound membership record; Fool can remain zero-valued
 
 | Manufactured care outcomes | Empress requires beneficiary-side verified change and per-beneficiary caps
 
 | Manufactured teaching outcomes | Hierophant requires demonstrated competence
 
 | Manufactured collaboration | Lovers requires a genuine joint result and self-dealing checks
 
 | Steamrolling | Chariot uses a net-of-harm condition
 
 | Conflict farming | Strength requires a non-party resolver and acceptance by both parties
 
 | Closure theater | Death requires released resources and preserved learning
 
 | Reflection theater | Hanged Man requires a changed decision
 
 | Balance theater | Temperance includes stall detection
 
 | False accusations | Devil requires corroboration and carries a governed false-claim penalty
 
 | Crisis creation | Tower pays response and stabilization, not detection or caused failure
 
 | Popularity capture | Sun is tied to an existing verified contribution
 
 | Reviewer capture | Independent review, rotation, conflict checks, appeal, and agreement metrics
 
 | AI leverage capture | A member with capital deploys large amounts of AI to flood verified contribution. Measurement and $RES pay the verified outcome in full, but the action record attributes the human and AI portions, voting salience counts only the human-attributed portion, per-member weight caps bound what remains, and Devil audits concentration of agent-amplified output as a capture vector
 
 | Scalar capture | $RCT never replaces the underlying 22-dimensional profile
 
 | Sector flattening | Sector views cannot rewrite dimension balances or $RCT

Devil and Tower bounty amounts remain subject to a separate, governed anti-farming policy. This appendix defines the control requirements, not the bounty values.

### A.7 Timing and cycle rules

Continuous credit applies when the outcome is immediately verifiable: accepted creation, durable record with verified use, demonstrated competence, accepted joint artifact, verified milestone, verified claim resolution, attributed recognition.

Cycle-gated credit applies when the result needs time or comparison: enabled growth, discovery that steers later work, a pioneering crossing validated by the followers it later attracts, attribution settled along provenance chains, healthy recurrence, fair accounting, sustainability, capture exposure, crisis stabilization, complete cycle reckoning, whole-system integration.

Judgement cannot finalize a cycle without a current Justice calibration and a readable audit record. A disputed action can remain pending into the next cycle without creating an automatic negative balance.

### A.8 Governed parameters

 | Parameter | Bootstrap authority | Post-transition authority

 | Dimension definitions and boundaries | Custodians publish version and rationale | DAO governance
 
 | Evidence standards | Custodians with community review | DAO governance
 
 | Normalization functions | Custodians within published bounds | DAO governance
 
 | $RCT aggregation weights | Custodians with audit | DAO governance
 
 | Review-tier thresholds | Custodians | DAO governance
 
 | Devil and Tower bounty caps | Conservative temporary cap pending anti-farming policy | DAO governance
 
 | Cycle length | Custodians with notice | DAO governance
 
 | Sector-view definitions | Interface maintainers under published schema | DAO governance if they affect rights or rewards
 
 | Voting weight functions and salience templates | Custodians with Devil audit | DAO governance

Justice and Judgement operators are human-only during bootstrap. AI agents may assist with proposals, evidence organization, and anomaly detection, but do not receive human-only governance rights by implication.

### A.9 The required explanation record

Every contribution decision that affects C_i, $RCT, or $RES must leave a record containing:

Explanation record
 
action_id · contributor_reference · evidence_references · active_dimensions · proposed_intensities · verified_intensities · outcome_value · verification_confidence · calibration_version · review_tier · review_decisions · contribution_deltas · res_payment_decision · appeal_status

With that record, a member can answer: what action was read, which dimensions activated and why, what evidence supported them, what value was assigned, who reviewed it, which calibration version was used, and what can be appealed or corrected.

Appendix B

## Open questions and deferred decisions

This specification is a working proposal. The decisions below are deliberately left open until the pilot produces evidence, and the risks below are the known ways the model could fail in practice.

### B.1 Deferred until the model is approved and tested

- Final contribution balance names or token tickers
 
- On-chain representation of the 22 balances; the ledger-first path is evaluated before tokenization
 
- The final $RCT formula, normalization, and recency treatment
 
- Exact sector projection definitions and their governance
 
- $RES emissions, bounty values, and timing
 
- UCI eligibility and the distribution formula
 
- Tenure treatment in calibration
 
- The provenance decay function: how much credit flows back along an informed-by chain, and how many links deep it settles
 
- The voting weight multiplier shape and the salience templates: which dimensions each class of question draws on, and how balances scale into weight
 
- The human and AI attribution method: how the split of a Human+AI action is declared, evidenced, and reviewed
 
- The size and governance of the Fool pool for portfolio bets on risky crossings

### B.2 Residual risks

- Scalar capture. $RCT may hide meaningful differences between profiles. Profile-aware governance is required.
 
- Reviewer bias. Human reviewers may reproduce cultural, linguistic, or status bias. Agreement and under-recognition audits are required.
 
- Visibility bias. The classifier may prefer visible artifacts over care, maintenance, or thought. The calibration corpus must include invisible work.
 
- Sector drift. Sector views may quietly become the real system through interface design. They must remain projections by contract and schema.
 
- Interpretive drift. Pollack's language may be treated as authority rather than a design resource. Evidence remains the basis for credit.
 
- Technical complexity. 22 separate balances may be more machinery than the pilot needs. The ledger-first path is evaluated first.
 
- Representation ambiguity. $RCT may end up a token, a derived index, or an attested balance. The public language must match whichever is approved.
 
- Provenance gaming. Informed-by chains could be stuffed with famous work to siphon downstream credit. Decay, Judgement settlement, and evidence that the influence was real bound this; the pattern needs testing in the pilot.
 
- Attribution gaming. A member may overstate the human portion of Human+AI work to convert agent output into voting weight. Review of the attribution, per-member weight caps, and Devil audits of concentration bound this; the split is not mechanically provable and must be tested in the pilot.
 
- Presence inflation. The presence clause widens what counts as an outcome; fabricated or reciprocal care claims are its abuse surface. Beneficiary-side evidence, the bar on mutual attestation alone, caps, and under-recognition audits bound it, and calibration must watch for inflation of presence claims over time.

### B.3 Implementation sequence

- Build the auditable action ledger.
 
- Implement the 22-dimensional candidate profile.
 
- Test deterministic verification for Tier 0 actions.
 
- Add selective human review for ambiguous actions.
 
- Measure inter-rater agreement and under-recognition.
 
- Calibrate normalization and dimension weights.
 
- Calculate a non-transferable $RCT index from the vector.
 
- Add $RES payments only after the evidence and fairness path works.
 
- Add optional sector projections for readability.
 
- Consider separate on-chain contribution tokens only after the accounting model survives the pilot.

### B.4 Acceptance tests

- All 22 foundations are represented, and a single action can activate several dimensions
 
- Fool measures every threshold crossing: entry may be zero-valued while a verified pioneering crossing earns value; Magician remains unified
 
- Sector views are not the source of truth; $RCT is generated from the 22-dimensional record
 
- $RES remains separate from contribution accounting
 
- Symbolic interpretation cannot create payment without evidence
 
- Human review is selective, explainable, conflict-checked, and appealable
 
- Unresolved judgment does not create an automatic negative contribution balance
 
- Unmeasured work is recorded as null, never silently as zero; late settlement pays back to when the work happened
 
- Invisible work is included in calibration and fairness audits
 
- No leaderboard ranks members by contribution
 
- Verified harm reduces or voids the outcome before any delta is calculated; no damaging result pays gross
 
- Chain settlement pays attribution back along informed-by links with governed decay, and no link pays without evidence of real influence
 
- Committed capital can earn contribution only as a recorded action with a verified outcome, never counts toward voting weight, and cannot claim both a $RES reward and contribution credit for the same commitment
 
- Voting salience counts only the human-attributed portion of balances; agent-performed work earns measurement and $RES but never becomes votes
 
- A third party can reproduce a contribution calculation from its record and calibration version

Appendix C

## Sources

This page is the canonical and single source of truth for the Contribution Economy. The earlier markdown drafts that preceded it are superseded and retained only as historical material.

#### Reference sources

- The Augmentatism Manifesto, the doctrine this economy aligns with: sovereign purpose, the Law of Anti-Capture, the Law of Creative Latency, cultivation, discovery, the Inter-Agent Economy, and community-verified integrity
 
- Fool's Journey, used as an interpretive sequence model (LearnTarot)
 
- Rachel Pollack, Seventy-Eight Degrees of Wisdom, used as an interpretive reference for symbolism, psychological meaning, relationships, and readings (publisher page)
 
- Carl Jung, 1933 lecture on the Tarot, the source of the archetypal reading of the Major Arcana used here: "psychological images, symbols with which one plays" (Carl Jung; documentation of the lecture at Mary K. Greer)
 
- thefool.im, a reading guide to the 22 Major Arcana as archetypes, used for the archetypal interpretation of the dimensions
 
- Thematic Apperception Test (TAT), developed by Henry A. Murray and Christiana D. Morgan at Harvard in the 1930s: the standard demonstration that people project their own situation into the story they tell about an ambiguous image (Wikipedia)
 
- Dan P. McAdams and Kate C. McLean, "Narrative Identity," Current Directions in Psychological Science (2013), on the life story as an internalized and evolving narrative that guides action (SAGE)
 
- Card images: Rider-Waite Tarot, illustrated by Pamela Colman Smith, published 1909, London; public domain, via Wikimedia Commons. The Tarot itself originated in 15th-century Italy; the 350-year reference in this page dates the standardized 22-card Major Arcana tradition.

Contribution Economy working specification v2. The map is a hypothesis; real activity tests the measurement.

ResonantDAO · Contribution Economy
 
The map is 22. The filling is open.
Contribution Economy working specification v2 ·

---

## Links on page

- ../
- ../start/
- ../learn/
- ../events/
- ../build/
- ../community/
- ../whitepaper/
- ./
- https://resonantos.com/
- https://en.wikipedia.org/wiki/Carl_Jung
- #abstract
- #intro
- #why22
- #ontology
- #payment
- #completeness
- #alignment
- #license
- #appendix-rules
- #why22-axes
- #why22-lock
- #ontology-grid
- #ontology-examples
- #payment-table
- #payment-human
- #alignment-pollack
- #appendix-open
- #sources
- https://thefool.im/
- example/
- https://augmentatism.com/
- https://www.learntarot.com/journey.htm
- https://redwheelweiser.com/book/seventy-eight-degrees-of-wisdom-9781578636655
- https://marykgreer.com/2008/04/18/carl-jung-on-the-major-arcana/
- https://en.wikipedia.org/wiki/Thematic_Apperception_Test
- https://journals.sagepub.com/doi/abs/10.1177/0963721413475622
