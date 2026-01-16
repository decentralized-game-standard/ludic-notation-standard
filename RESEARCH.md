# Ludic Structures: Research Notes

**Working document for vocabulary and formalism development — 2026-01-15**

---

## Core Insight: Games as Conversations

Interactive systems are conversations between player and system. This is not metaphor—it is structure:

1. Player observes state (listens)
2. Player formulates action (thinks)
3. Player performs verb (speaks)
4. System interprets verb (listens)
5. System applies rules (thinks)
6. System updates state (speaks)
7. Repeat

This maps directly to Chris Crawford's conversation model for interactivity and to information-theoretic feedback loops. A game's quality correlates with its conversational quality: clear listening, meaningful vocabulary, coherent responses.

---

## The Primitives

Ludic Structures uses four primitives derived from Petri nets, reframed for game design:

| Primitive | Definition | Conversation Analog |
|-----------|------------|---------------------|
| **State** | A condition or situation that can be observed | "What is being communicated" |
| **Verb** | An action available to an agent | "What can be said in response" |
| **Arc** | A unidirectional connection enabling flow | "The channel of exchange" |
| **Mark** | Current presence or quantity at a State | "What's active right now" |

### State
A State represents a game condition: resources held, positions occupied, flags set, phases active. States are observable (with possible visibility restrictions per agent).

### Verb
A Verb represents an action an agent can take. Verbs consume/produce Marks and may have preconditions (Arcs from States that must hold Marks). The richness of a game's Verb vocabulary determines its conversational bandwidth.

### Arc
Arcs are **unidirectional**. Their meaning is inferred from position:
- **State → Verb**: The player observes this State to inform their Verb choice (input/reading)
- **Verb → State**: The system updates this State in response to the Verb (output/writing)

Arcs may carry conditions (minimum Marks required) or effects (Marks consumed/produced).

### Mark
A Mark indicates current presence at a State. Multiple Marks may occupy a State (quantities). Marks flow through the structure as Verbs fire.

---

## Composition: Building Up, Not Breaking Down

Structures compose from primitives:

| Level | Description | Linguistic Analog |
|-------|-------------|-------------------|
| **Primitive** | State, Verb, Arc, Mark | Letter |
| **Atom** | Minimal complete loop: State → Verb → State | Word |
| **Pattern** | Named, reusable cluster of atoms | Phrase |
| **Score** | Complete game structure | Composition |

Composition is additive. Patterns are built from atoms. Scores are built from patterns. At every level, the same four primitives apply—we're always looking at States, Verbs, Arcs, and Marks.

### Pattern Library (conceptual)
Common patterns that transfer between games:
- **Resource Conversion**: Consume Marks from one State, produce Marks at another
- **Gating**: Verb requires Marks at prerequisite State
- **Accumulation**: Marks build up until threshold triggers Verb
- **Depletion**: Finite Marks consumed over time
- **Cycle/Loop**: Marks flow in circuit, enabling repetition

---

## Multiplayer: System as Mediator

Players never interact directly. All interaction is **system-mediated**:

- Player A performs Verb → System updates State → Player B observes State
- There are no player-to-player Arcs

Multiplayer modeling requires:
1. **Multiple Verb sets**: Each agent has available Verbs (possibly overlapping)
2. **Visibility masks**: Each agent sees a subset of States/Marks
3. **Turn/phase structure**: Rules governing when each agent can perform Verbs

"Bluffing" is not a primitive—it emerges from differential visibility. Both players share access to some States (pot, community cards) while holding private States (their hand). The bluff is a Verb choice made with private information.

---

## Design Use Case

A designer sketching a new game:

1. Define the **core dialogue**: "Player chooses tile. Board cascades. Player observes and chooses again."
2. Diagram the States: board configuration, score, available moves
3. Diagram the Verbs: tile swap, special ability, end turn
4. Draw the Arcs: What does the player observe before choosing? What does each Verb change?
5. Identify gaps: "Where does the player learn valid moves? I need an Arc from BoardState into visibility."
6. Extend with Patterns: "Add a boss mechanic—its own States, Verbs, and a Pattern for vulnerability windows."

The diagram becomes a compositional sketch. Relationships are explicit. Missing feedback loops are visible.

---

## Analysis Use Case: Slay the Spire

Reverse-engineering an existing game:

**Core loop atoms:**
- Draw cards (Verb) → Hand State gains Marks → Player observes Hand
- Play card (Verb, requires Energy + Card in Hand) → Effects fire → Enemy HP, Block, Buffs update
- End turn (Verb) → Enemy acts (system Verb) → Player HP updates → Discard → Draw

**Nested loops:**
- Combat: Many Draw/Play/EndTurn atoms until Enemy HP = 0 or Player HP = 0
- Run: Many Combats with Map choices, Shop visits, Rest sites
- Meta: Many Runs, unlocking cards/characters

**Why it works:**
- Visible enemy intents = clear State observation before Verb choice
- Card synergies = rich Verb vocabulary with emergent combinations
- Nested timescales = mastery at multiple levels (single card → single combat → run strategy)

**A failed design would show:**
- Ambiguous feedback (unclear State updates after Verb)
- Narrow Verb vocabulary (few meaningful choices)
- Missing Arcs (player can't observe what they need to decide)

---

## Open Questions

1. **Temporal structure**: How to notate real-time vs. discrete turns? Simultaneity? Interruptibility?
2. **Probability/randomness**: Arcs with probabilistic outcomes? States with hidden distributions?
3. **Hidden information**: Visibility masks are per-agent, but how to notate cleanly?
4. **Feedback intensity**: A Mark is binary (present) or quantitative (count). How to capture *magnitude* of feedback?
5. **Diagram notation**: What visual conventions? Color? Iconography? Spatial layout?

---

## Influences and Lineage

- **Petri nets** (Carl Adam Petri, 1962): Mathematical formalism for concurrent processes
- **Chris Crawford**: Conversation model of interactivity
- **Raph Koster / Daniel Cook / Stephane Bura**: Game grammar, skill atoms, game diagrams
- **Cameron Browne / Ludii**: Ludeme-based game description language
- **Project Horseshoe 2008**: Multiplayer skill atoms, system-mediated interaction

---

## Next Steps

1. Stress-test vocabulary by diagramming 2-3 games (simple, complex, multiplayer)
2. Identify missing primitives or refinements
3. Develop visual notation conventions
4. Draft updated README.md for ludic-standard

---

*This is a working document. Everything is provisional.*
