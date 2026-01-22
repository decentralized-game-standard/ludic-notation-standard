# Ludic Structures ("The Notation")

🏠 **[Overview](https://github.com/decentralized-game-standard)** · 🔧 **[RUNS](https://github.com/decentralized-game-standard/runs-standard)** · 📦 **[AEMS](https://github.com/decentralized-game-standard/aems-standard)** · ⚡ **[WOCS](https://github.com/decentralized-game-standard/wocs-standard)** · 🎭 **[Ludic Structures](https://github.com/decentralized-game-standard/ludic-standard)** · ❓ **[FAQ](https://github.com/decentralized-game-standard/.github/blob/main/profile/FAQ.md)**

> **Status**: Draft / RFC  
> **Version**: 0.1.0

---

Games are structured conversations between agent and world.

The **Ludic Structures Standard** provides a neutral, implementation-agnostic notation for describing these conversations—the core mechanics and rules of interactive systems. While RUNS defines *how* a game executes each frame through data flow, and AEMS defines *what* entities participate, Ludic Structures defines the underlying *grammar*: the states, actions, transitions, and resources that shape meaningful play.

Without a shared notation, mechanics remain embedded in proprietary code—fragile, unpreservable, and locked to one implementation. This standard breaks that pattern by treating rules as portable, analyzable artifacts: open rulebooks that can outlive any single engine or company.

Think of it as sheet music for gameplay: sufficient to convey the interactive structure clearly, leaving performance (execution, visuals, timing) to specialized interpreters.

## Why a Shared Notation?

Traditional games fuse rules into executable code, making them:

- **Opaque** — Hard to analyze, balance, or teach without playing.
- **Fragile** — Updates or ports risk altering core feel.
- **Proprietary** — Genres die when companies abandon them.

Ludic Structures enables:

- **Portable Genres** — Define a MOBA or roguelike once; evolve it community-wide.
- **Rapid Composition** — Build novel games by remixing proven patterns.
- **Preservation** — Mechanics as cultural artifacts, versioned and forkable.
- **Analysis** — Formal tools for studying emergence, fairness, or accessibility.

This notation is deliberately abstract and minimal: it captures interaction without prescribing real-time flow, probability, rendering, or platform.

## Core Primitives

These four primitives form the atomic units of any interactive structure.

| Primitive | Purpose                              | Key Attributes                                      | Notes                              |
|-----------|--------------------------------------|-----------------------------------------------------|------------------------------------|
| **State** | Observable condition or situation    | ID, description, visibility rules                   | The "nouns" of position in play   |
| **Verb**  | Available action or affordance       | ID, preconditions (Arcs in), effects (Arcs out)      | The bridge from observation to agency |
| **Arc**   | Directed transition or requirement   | Source → Target, min Marks, guard expressions, consume/produce Marks | The "rules" governing flow        |
| **Mark**  | Token or quantifiable resource       | Type, quantity (numeric or structured)              | Tracks progress, inventory, score |

### Detailed Definitions

**State**  
- Represents any distinguishable situation (e.g., "door_locked", "player_alive").  
- Visibility can mask information for hidden states.

**Verb**  
- Represents an action the agent can attempt (e.g., "open", "attack").  
- Enabled when all precondition Arcs are satisfied (including any guards). Multiple enabled Verbs preserve interactive choice.

**Arc**  
- The directional link defining legality and consequences.  
- May require a minimum quantity of Marks, include a **guard** (boolean expression evaluated over current Marks, e.g., `strength >= 5` or `inventory.keys >= 1`), and consume/produce Marks.  
- Guards enable compact representation of ranged conditions without state explosion, while remaining decomposable to pure Petri-net forms.

**Mark**  
- Quantifiable resources or tokens placed on States (e.g., `health=3`, `ammo=10`, `strength=7`).  
- Marks support numeric values and accessor syntax in guards (e.g., `inventory.bombs >= 1`), allowing future extension to structured data without changing primitives.

## Composition Hierarchy

Primitives combine into higher-level reusable units.

| Level     | Definition                           | Purpose                                             | Example                            |
|-----------|--------------------------------------|-----------------------------------------------------|------------------------------------|
| **Atom**  | Minimal closed interactive loop      | Smallest meaningful unit (often State → Verb → State)| "Press button → receive reward"   |
| **Pattern**| Named, versioned cluster of Atoms/primitives | Reusable mechanic with defined interface            | "Locked Door", "Basic Combat"     |
| **Score** | Root composition importing Patterns  | Full game ruleset, defining complete Capability/Content split | "Chess Rules v1.0", "Simple Platformer" |

Patterns support extension and forking semantics (e.g., `extends: ludic:open-door@1.0` with modifications).

## Schema Separation

Ludic Structures enforces a clean division:

- **Capability Schema** — What the agent can do (Verbs and preconditions).
- **Content Schema**   — What the world affords (States, Arcs, Marks available).
- **Content Instance** — Specific arrangement (e.g., level layout placing Marks).
- **Play State**       — Runtime-only (current position)—excluded from notation.

This separation prevents conflating rules with specific playthroughs.

## Usage Guidelines

Implementers **SHOULD** adhere exactly to primitives and composition rules for interoperability. Tools can validate Scores, generate diagrams, or export to RUNS Networks.

**Guards in Practice**  
Guards are evaluated at enablement time using current Marks. Supported operations include comparisons (`>=`, `==`, etc.), arithmetic, and logical connectors. This keeps the notation compact for gradients (health ranges, inventory counts) while preserving non-deterministic choice when multiple Verbs are enabled.

Example minimal Score (YAML sketch) showing arc-level guards:
```yaml
version: 1.0
imports:
  - ludic:open-door@1.0

states:
  - id: near_door
  - id: door_open

marks:
  - type: strength
    default: 3
  - type: inventory.keys
    default: 0

verbs:
  - id: open_door
    preconditions:
      - arc:
          source: near_door
          # Simplified effect for demo
    effects:
      - arc:
          target: door_open

  - id: kick_door
    preconditions:
      - arc:
          source: near_door
          guard: strength >= 5

extends:  # Shorthand form (equivalent to adding a guarded precondition arc)
  open-door:
    add_verb: kick
    precondition: strength >= 5
```

## What the Notation Deliberately Excludes

| Excluded                | Why                                          | Where It Belongs                     |
|-------------------------|----------------------------------------------|--------------------------------------|
| Timing / real-time flow | Varies by genre (turn-based vs. continuous)  | RUNS execution / runtime             |
| Probability & RNG       | Implementation detail                        | Specific Processors                  |
| Visuals / audio         | Presentation layer                           | AEMS assets / rendering Processors   |
| Hidden implementation   | Notation must be complete and transparent    | Forbidden                            |
| Platform specifics      | Keeps rules neutral                          | Runtime bindings                     |

These exclusions ensure the notation remains a true abstract grammar rather than a biased framework.

## Integration with the Decentralized Game Standard

| Layer                   | Role                                         | Connection to Ludic Structures               |
|-------------------------|----------------------------------------------|---------------------------------------------|
| **AEMS**                | Autonomous entities and assets               | Manifestations provide the "nouns" (e.g., keyed doors) |
| **RUNS**                | Execution and data flow                      | Scores export to Networks of Records/Processors |
| **WOCS**                | Coordination and ecosystem                   | Registry for Patterns/Scores, bounties for pairings |
| **Ludic Structures**    | Notation for mechanics                       | The shared rulebook layer                    |

A Score becomes executable when paired with RUNS implementations of its Patterns.

## Summary

Ludic Structures turns fleeting code into enduring rulebooks—open, composable, and preservable.

Shared notation enables vast innovation.

Small primitives enable rich conversations.

**MIT License** — Open for implementation, extension, critique.