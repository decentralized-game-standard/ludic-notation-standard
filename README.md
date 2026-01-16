# Ludic Structures

**A Compositional Notation for Interactive Rule Systems — Conceptual Exploration, 2026-01-16**

🏠 **[Overview](https://github.com/decentralized-game-standard)** · 📦 **[AEMS](https://github.com/decentralized-game-standard/aems-standard)** · 🔧 **[GERS](https://github.com/decentralized-game-standard/gers-standard)** · ⚡ **[WOSS](https://github.com/decentralized-game-standard/woss-standard)** · ❓ **[FAQ](https://github.com/decentralized-game-standard/.github/blob/main/profile/FAQ.md)**

---

Physical games have rulebooks. Chess exists as a document anyone can read, interpret, and implement with whatever pieces are at hand. The rules are separate from the board, separate from the players, separate from any particular match. This separation enables analysis, comparison, preservation, and infinite variation.

Digital games fuse rules into code. The "rulebook" is scattered across scripts, embedded in engines, locked behind compilation. You cannot read the rules of a digital game the way you can read the rules of chess. You cannot compare two games structurally without reverse-engineering both. You cannot preserve the rules when the engine dies.

Ludic Structures proposes a notation for interactive rule systems—a way to capture the structural essence of games in a form that is readable, composable, and portable. Like musical notation captures compositions without producing sound, Ludic Structures captures game mechanics without executing them. The goal is a formalism useful for design sketching, analytical comparison, and cross-engine portability.

This applies to all interactive systems: physical sports, board games, digital games, authored narrative experiences. The same primitives describe football and The Last of Us; the difference is scope and composition.

---

## Core Insight: Games as Conversations

Interactive systems are dialogues between player and system:

1. System presents state (speaks)
2. Player observes state (listens)
3. Player formulates response (thinks)
4. Player performs verb (speaks)
5. System interprets verb (listens)
6. System applies rules (thinks)
7. System updates state (speaks)
8. Repeat

This is not metaphor—it is information-theoretic structure. A game's quality correlates with its conversational quality: clear communication, meaningful vocabulary, coherent responses to input. A game fails when the dialogue breaks down: ambiguous feedback, unlearnable verbs, arbitrary state changes.

The formalism captures this dialogue structure explicitly.

---

## The Primitives

Four primitives describe all interactive structures:

| Primitive | Definition | Conversation Analog |
|-----------|------------|---------------------|
| **State** | A condition that can be observed | What is being communicated |
| **Verb** | An action available to an agent | What can be said in response |
| **Arc** | A unidirectional connection enabling flow | The channel of exchange |
| **Mark** | Current presence or quantity at a State | What's active right now |

### State
A State represents a game condition: resources held, positions occupied, flags set, phases active. States are observable (with possible visibility restrictions per agent). Examples: HP remaining, room currently occupied, enemy alertness level.

### Verb
A Verb represents an action an agent can take. Verbs have preconditions (Arcs from States that must hold Marks) and effects (Arcs to States that gain or lose Marks). The richness of a game's Verb vocabulary determines its conversational bandwidth. Examples: swing sword, open door, place bid.

### Arc
Arcs are **unidirectional**. Their meaning is inferred from position:
- **State → Verb**: Input—the agent observes this State to inform action
- **Verb → State**: Output—the system updates this State in response

Arcs may carry conditions (minimum Marks required to fire) or effects (Marks consumed/produced).

### Mark
A Mark indicates current presence at a State. Multiple Marks may occupy a State (representing quantities). Marks flow through the structure as Verbs fire. Examples: 3 Marks at "health" State, 1 Mark at "has_key" State.

---

## Composition: Primitives to Genres

Structures compose hierarchically:

| Level | Description | Composed From |
|-------|-------------|---------------|
| **Primitive** | State, Verb, Arc, Mark | Irreducible |
| **Atom** | Minimal complete loop | Primitives |
| **Pattern** | Named, reusable cluster | Atoms |
| **Score** | Complete game structure | Patterns |

An Atom is the smallest complete dialogue turn: State → Verb → State. "Player sees door, player opens door, door state changes."

A Pattern is a named composition: "open-door" as a reusable structure. Patterns can be imported, extended, and combined.

A Score is a complete game: all Patterns composed into the full interactive structure.

This enables POSIX-style reuse:

```yaml
dependencies:
  - ludic:open-door@1.0
  - ludic:combat-melee@2.1
  - ludic:inventory-grid@1.3

extends: ludic:open-door@1.0
modifications:
  - auto_walk_through: true
```

The "open door" mechanic in Ocarina of Time and Majora's Mask is identical—the same Pattern. Half-Life: Alyx has a more complex door-opening Pattern. Both reference the same conceptual atom but extend it differently.

---

## Schema vs. Instance

Ludic Structures operate at distinct levels:

| Level | What it defines | Reusability |
|-------|-----------------|-------------|
| **Capability Schema** | All Verbs an agent can perform | Reusable across instances |
| **Content Schema** | What kinds of world elements exist | Reusable across instances |
| **Content Instance** | A specific arrangement | Unique composition |
| **Play State** | Current Marks during a session | Runtime only |

### Capability Schema
The full set of Verbs available to an agent: their preconditions, effects, and interconnections.

*Example: Link's moveset in Ocarina of Time—sword swing, shield, roll, Z-target, bomb, hookshot. This schema is defined once and travels with Link through every dungeon.*

### Content Schema
What kinds of things can exist in the world and how they interface with capabilities.

*Example: "Dungeons contain rooms. Rooms connect via doors. Rooms can contain enemies, puzzles, chests. Enemies have HP and vulnerability types. Locked doors require keys."*

### Content Instance
A specific authored arrangement of Content Schema elements.

*Example: The Deku Tree—these specific rooms, these connections, these enemies, these puzzles.*

### Play State
The current Marks flowing through the structure during a session.

*Example: Link is in room 3, has 2 hearts, has killed Deku Baba #1, has not yet obtained the slingshot.*

---

## The Sphere-on-Surface Model

A mental model for Capability and Content interaction:

**Capability Schema as Sphere**: All the player's Verbs exist on the surface of a sphere, interconnected by internal logic. The sphere is self-contained and portable—the player brings their entire capability set into every situation.

**Content Instance as Surface**: The game world laid out topologically. The surface has interface points where specific capabilities can connect.

**Traversal**: The sphere rolls along the surface. At any moment, only compatible capabilities are "grounded"—touching applicable content. Walk into a room with an enemy → sword and shield become relevant. Walk to a bombable wall → bomb becomes relevant.

This captures: **capabilities are always present but conditionally relevant**.

---

## Applicability

The same primitives describe all interactive systems:

| System | Capability Schema | Content Schema | Content Instance |
|--------|-------------------|----------------|------------------|
| **Chess** | Piece movement rules | "Board has squares, pieces can be captured" | Initial board setup |
| **Football** | Player movement, ball handling, tackling | Field zones, goal scoring, fouls | A specific match |
| **Ocarina of Time** | Link's full moveset | Dungeon grammar, enemy types, item gates | The Deku Tree |
| **The Last of Us** | Joel's verbs (stealth, combat, crafting) | Level types, enemy behaviors, resource spawns | A specific chapter |
| **Poker** | Bet, fold, raise, check | Hand rankings, round structure, pot rules | A specific hand |

Physical chess and digital chess share identical Capability and Content Schemas—the difference is whether physics (piece movement) is assumed from reality or explicitly simulated. Football has a small Content Instance (one field, one ball) but rich Capability Schema (many legal actions). The Last of Us has enormous Content Instance (authored levels, scripted encounters) with smaller Capability Schema.

---

## Use Cases

### Design: Sketching Before Prototyping

Games have massive possibility spaces. It's hard to see implications and relationships without prototyping—a time-consuming process.

Ludic Structures enables structural sketching:
1. Define the core dialogue: "Player sees tile, player swaps, board cascades, player observes result"
2. Diagram the Capability Schema: What Verbs? What preconditions?
3. Diagram the Content Schema: What State types exist?
4. Identify missing feedback loops: "Where does the player learn valid swaps?"
5. Import proven Patterns: `match-three@1.0`, extend with custom cascade rules
6. **Then** prototype with structural confidence

### Analysis: Seeing What Surface Obscures

Why does Portal feel innovative? A Ludic Structures analysis reveals:
- **Capability Schema**: Novel (portal gun introduces new spatial verbs)
- **Content Schema**: Familiar (gated progression, puzzle rooms)
- Same engine could be a shooter—the ludic innovation is in the Capability Schema.

Why do Ocarina of Time and Breath of the Wild feel so different?
- **Capability Schema**: Radically different (climbing, cooking, weapon durability vs. item-gated tools)
- **Content Schema**: Different (open world vs. dungeon progression)

These comparisons are impossible with vibes-based criticism. The formalism makes structure visible.

### Preservation: Rules That Outlive Engines

If a game's Ludic Structure is published, the rules survive engine death. Future engines can re-interpret the structure faithfully or creatively.

This complements:
- **AEMS**: Entities persist (the sword exists)
- **GERS**: Engines compose (the renderer changes)
- **Ludic Structures**: Rules endure (the combat system is documented)

---

## Integration with the Standard

| Protocol | What it captures | Ludic Structures relationship |
|----------|------------------|-------------------------------|
| **AEMS** | What entities *are* | Entities flow through Ludic Structures as Marks |
| **GERS** | How engines *execute* | Processors implement Ludic Structure Verbs |
| **WOSS** | How coordination *flows* | Funds creation and curation of Patterns |

A community publishes a "MOBA" Score as a Ludic Structure. AEMS defines the heroes and items as Entities. GERS engines implement the Verbs. WOSS bounties fund new Patterns. Different clients interpret the same structure—minimalist for pickup games, polished for tournaments.

---

## The Promise

1. **The Napkin Sketch That Works** — Diagram game ideas structurally. See feedback loops, identify gaps, compare to known patterns—before writing code.

2. **Genre as Shareable Artifact** — "MOBA" or "Zelda-like" becomes a diffable schema, not a vibes-based label. Fork it, version it, extend it explicitly.

3. **Analysis That Reveals** — See why games work or fail structurally. Compare innovations across titles. Identify the atom-level differences beneath surface presentation.

4. **Pattern Library as Public Good** — Common mechanics become shared infrastructure. Import "open-door," "cooldown-timer," "inventory-grid." Extend, customize, contribute.

5. **Preservation** — Rules survive engine death. The Ludic Structure of a beloved game can be re-implemented faithfully by future engines.

---

## Status and Scope

This is early-stage formalism—deliberately minimal and uncommitted. The primitives feel right for expressiveness and composition, but alternatives may emerge. Open questions remain:

- Temporal structure (real-time vs. discrete, simultaneity)
- Probability and randomness (stochastic Arcs)
- Hidden information (per-agent visibility masks)
- Visual notation conventions

The focus is enabling open rulebooks as cultural artifacts, complementing AEMS (entities), GERS (engines), and WOSS (coordination). Experiment. Diagram a favorite game. See what breaks.

---

## Prototype Path

1. **Diagram on Paper** — Pick a simple game, identify States, Verbs, Arcs, Marks
2. **Identify Patterns** — Name the reusable atoms and clusters
3. **Compare to Another Game** — Same Pattern, different Parameters? Different Pattern entirely?
4. **Attempt a Genre Schema** — Can "Zelda-like" be defined as a composable structure?
5. **Contribute Patterns** — Publish named, versioned Patterns for community reuse

---

**MIT License** — Free to use, extend, critique, implement.
