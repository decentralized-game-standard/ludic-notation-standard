# Ludic Structures: Research Notes

**Working document for vocabulary and formalism development — 2026-01-16**

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

## Schema vs. Instance

Ludic Structures operate at two distinct levels:

| Level | What it defines | Reusability |
|-------|-----------------|-------------|
| **Schema** | Types, rules, interface definitions | Reusable across instances |
| **Instance** | Specific authored arrangements | Unique compositions |

This distinction applies to both capability and content:

### Capability Schema
The full set of Verbs available to an agent, their interconnections, preconditions, and effects. Defined once, applicable everywhere.

*Example: Link's moveset in Ocarina of Time—sword swing, shield, roll, Z-target, item use. This schema travels with Link through every dungeon.*

### Content Schema
What kinds of world elements can exist and how they interface with capabilities. The vocabulary of "things that can be in a level."

*Example: "Dungeons contain rooms. Rooms connect via doors. Rooms can contain enemies, puzzles, chests. Enemies have HP and are vulnerable to specific Verbs. Locks require keys."*

### Content Instance
A specific authored arrangement of Content Schema elements.

*Example: The Deku Tree—15 rooms, these specific connections, these enemy placements, these puzzles.*

### Play State
The current Marks flowing through the structure during a session.

*Example: Link is in room 3, has 2 hearts, has killed Deku Baba #1, has not yet obtained the slingshot.*

---

## The Sphere-on-Surface Model

A mental model for how Capability and Content interact:

**Capability Schema as Sphere**: All the player's Verbs exist as nodes on the surface of a sphere, interconnected by internal logic (Z-target enables jump attack, bomb requires bomb count > 0). The sphere is self-contained and portable—the player brings their whole capability set into every situation.

**Content Instance as Surface**: The game world laid out topologically—rooms, enemies, puzzles, gates. The surface has **interface points** where specific capabilities can connect (this crack is bomb-compatible, this enemy is sword-compatible).

**Traversal**: The sphere "rolls" along the surface. At any moment, only compatible capabilities are "grounded"—touching applicable content. Walk into a room with a Deku Baba → sword and shield verbs become relevant. Walk to a bombable wall → bomb verb becomes relevant. The rest of the sphere exists but isn't engaged.

This captures: **capabilities are always present but conditionally relevant**. The graph doesn't change room-to-room, but which parts are active does.

---

## Composition: POSIX-Style Building Blocks

Structures compose from primitives like POSIX utilities compose from syscalls:

| Level | Description | Analog |
|-------|-------------|--------|
| **Primitive** | State, Verb, Arc, Mark | Syscalls |
| **Atom** | Minimal complete loop: State → Verb → State | Core utilities (read, write) |
| **Pattern** | Named, reusable cluster of atoms | Composed utilities (grep, sed) |
| **Score** | Complete game structure | Full application |

### Package-Style Reuse

Patterns should be importable and extensible, like dependency packages:

```yaml
# Example: A game importing ludic patterns
dependencies:
  - ludic:open-door@1.0
  - ludic:combat-melee@2.1
  - ludic:inventory-grid@1.3
```

### Extension and Variation

Base patterns can be extended for specific needs:

```yaml
# Example: Extending a base pattern
extends: ludic:open-door@1.0
modifications:
  - auto_walk_through: true    # OoT-style seamless transition
  - animation_lock: 0.5s       # Brief state lockout
```

This enables:
- **Cross-game analysis**: OoT and Majora's Mask share `open-door@1.0`, but MM adds `mask-transformation@1.0`
- **Design efficiency**: Import proven patterns, customize as needed
- **Portability**: The promise of the standard—rulesets that travel between engines

### Pattern Library (conceptual)

Common patterns that transfer between games:
- **Resource Conversion**: Consume Marks from one State, produce at another
- **Gating**: Verb requires Marks at prerequisite State
- **Accumulation**: Marks build until threshold triggers Verb
- **Depletion**: Finite Marks consumed over time
- **Cycle/Loop**: Marks flow in circuit, enabling repetition
- **Lock-and-Key**: State change requires specific prior State
- **Cooldown**: Verb disables temporarily after use

---

## Multiplayer: System as Mediator

Players never interact directly. All interaction is **system-mediated**:

- Player A performs Verb → System updates State → Player B observes State
- There are no player-to-player Arcs

Multiplayer is simply multiple agents with:
1. **Separate Capability Schemas**: Each agent's available Verbs (possibly overlapping)
2. **Shared Content**: States that multiple agents can observe/affect
3. **Visibility masks**: Each agent sees a subset of States/Marks
4. **Turn/phase structure**: Rules governing when each agent can perform Verbs

"Bluffing" is not a primitive—it emerges from differential visibility. Both players share access to some States (pot, community cards) while holding private States (their hand). The bluff is a Verb choice made with private information.

---

## Analytical Power

This framing enables systematic comparison:

### Schema-Level Game Comparison

| Comparison | Capability Schema | Content Schema | Insight |
|------------|-------------------|----------------|---------|
| OoT vs. MM | Nearly identical (same Link) | Similar + owl statues, time limits | MM's innovation is content/meta, not capability |
| OoT vs. BotW | Radically different (climbing, cooking, durability) | Different (open world vs. dungeon-gated) | BotW redefined both layers |
| OoT vs. Portal | Different verbs | Similar gating logic | Portal's innovation is capability, not content structure |

### Genre as Shared Schema

- **Zelda-like**: Capability Schema with item-gated progression; Content Schema with dungeon/overworld split
- **Soulslike**: Content Schema with bonfire checkpoints, corpse runs; Capability Schema with stamina management
- **Roguelike**: Content Schema with permadeath, procedural instances; Capability Schema with build diversity

### Isolating Design Innovations

When analyzing what makes a game different:
1. Map its Capability Schema (what can player do?)
2. Map its Content Schema (what kinds of things exist?)
3. Compare to genre baseline
4. The delta reveals the innovation

---

## Use Cases

### Design: Sketching Before Prototyping

1. Define the **core dialogue**: "Player chooses tile. Board cascades. Player observes and chooses again."
2. Diagram the Capability Schema: What Verbs? What preconditions?
3. Diagram the Content Schema: What State types? What interfaces?
4. Identify gaps: "Where does the player learn valid moves?"
5. Extend with Patterns: Import `match-three@1.0`, add custom cascade rules
6. **Then** prototype with confidence in the structure

### Analysis: Reverse-Engineering Existing Games

**Example: Slay the Spire**

*Capability Schema:*
- Verbs: Draw, Play Card, End Turn, Use Potion
- Preconditions: Play Card requires Energy ≥ cost, Card in Hand
- Internal logic: Deck defines available Play verbs

*Content Schema:*
- States: HP, Block, Energy, Deck/Hand/Discard, Buffs/Debuffs, Enemy Intents
- Interfaces: Cards affect HP/Block/Buffs; Enemies threaten HP

*Content Instance:*
- This specific run: Act 1, facing Cultist, current deck composition

*Why it works:*
- Visible enemy intents = clear State observation before Verb choice
- Card synergies = rich Verb vocabulary with emergent combinations
- Nested timescales = mastery at card/combat/run levels

---

## Open Questions

1. **Temporal structure**: How to notate real-time vs. discrete turns? Simultaneity? Interruptibility?
2. **Probability/randomness**: Arcs with probabilistic outcomes? States with hidden distributions?
3. **Hidden information**: Visibility masks are per-agent, but how to notate cleanly?
4. **Feedback intensity**: Marks capture presence/quantity, but not *magnitude* of experience
5. **Diagram notation**: Visual conventions? Color? Iconography? Spatial layout?
6. **Interface specification**: How to formally define Capability↔Content interfaces?

---

## Influences and Lineage

- **Petri nets** (Carl Adam Petri, 1962): Mathematical formalism for concurrent processes
- **Chris Crawford**: Conversation model of interactivity
- **Raph Koster / Daniel Cook / Stephane Bura**: Game grammar, skill atoms, game diagrams
- **Cameron Browne / Ludii**: Ludeme-based game description language
- **Project Horseshoe 2008**: Multiplayer skill atoms, system-mediated interaction
- **POSIX**: Composable primitives, pipes, the Unix philosophy

---

## Next Steps

1. Stress-test vocabulary by diagramming 2-3 games with clear Capability/Content separation
2. Draft interface specification format
3. Prototype a minimal Pattern package format
4. Develop visual notation conventions
5. Draft updated README.md for ludic-standard

---

*This is a working document. Everything is provisional.*
