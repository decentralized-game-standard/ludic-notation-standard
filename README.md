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
| **Arc**   | Directed transition or requirement   | Source → Target, conditions (min Marks), effects (consume/produce Marks) | The "rules" governing flow        |
| **Mark**  | Token or quantifiable resource       | Type, quantity                                      | Tracks progress, inventory, score |

### Detailed Definitions

**State**  
- Represents any distinguishable situation (e.g., "door_locked", "player_alive").  
- Visibility can mask information for hidden states.

**Verb**  
- Represents an action the agent can attempt (e.g., "open", "attack").  
- Exists only when reachable via Arcs from current States.

**Arc**  
- The directional link defining legality and consequences.  
- May require/consume Marks (e.g., "needs key") or produce them (e.g., "gain points").

**Mark**  
- Simple counters or tokens placed on States (e.g., health=3, ammo=10).

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

Example minimal Score (YAML sketch):
```yaml
version: 1.0
imports:
  - ludic:open-door@1.0
extends:
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