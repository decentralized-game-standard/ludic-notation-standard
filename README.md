# MAPS: Marks, Actions, Patterns, Scores

🏠 **[Overview](https://github.com/decentralized-game-standard)** · 🔧 **[RUNS](https://github.com/decentralized-game-standard/runs-standard)** · 📦 **[AEMS](https://github.com/decentralized-game-standard/aems-standard)** · ⚡ **[WOCS](https://github.com/decentralized-game-standard/wocs-standard)** · ❓ **[FAQ](https://github.com/decentralized-game-standard/.github/blob/main/profile/FAQ.md)**

> **Status**: Draft / RFC
> **Version**: 0.1.0

Four molecules encode every living thing on Earth. Adenine, thymine, guanine, cytosine. Four letters. They combine into triplets called codons. Codons chain into genes. Genes compose into genomes. From four primitives: bacteria, ferns, octopuses, human beings. The genome does not *build* the organism. It describes the structure, and the cell's machinery reads the description and builds from it. A genome written on paper is not alive. A genome loaded into a cell produces a living system. The notation is not the organism. The notation is what makes the organism reproducible, analyzable, and evolvable.

The key property is not complexity. It is *composability from a small alphabet*. Four bases produce three billion base pairs in a human genome. The bases do not know what they will build. They provide the grammar. The cell provides the interpreter. Every organism that has ever existed was produced by the same four-letter alphabet read by different machinery.

Game rules today have no genome. The mechanics of a game — the states a player can be in, the actions available, the transitions between them, the resources that flow through the system — exist inside compiled executables, tangled with rendering code and physics engines and network protocols. To understand what a game's rules *are*, you must either play the game or reverse-engineer the binary. When a studio dissolves, the rules are not preserved as readable text. They are lost inside dead code. Each development team invents the same mechanical patterns from scratch, because the previous team's patterns were never written down in a form anyone else could read.

What if every game had a genome?

MAPS is a four-primitive alphabet for describing the interactive grammar of a game. States, Verbs, Arcs, and Marks combine into Atoms, Atoms into Patterns, Patterns into Scores. A Score is a complete game ruleset, readable by any engine, forkable by any community, preservable across any span of time. The way DNA is readable by any cell, forkable by any mutation, and preservable across geological epochs.

## Four Primitives

**State** is a distinguishable condition. "Door locked." "Player alive." "Boss phase two." States are the positions in the game's landscape — the recognizable configurations a player can observe. In a card game: "your hand contains three aces" is a State. A State can be visible to all players or hidden.

**Verb** is an available action. "Open." "Attack." "Cast." "Fold." Verbs are the transitions a player can initiate from the current State. In a card game: "play a card" is a Verb. A Verb is enabled when its preconditions are satisfied. When multiple Verbs are enabled simultaneously, the player has a meaningful choice.

**Arc** is a directed connection between States and Verbs that defines what is legal and what it costs. An Arc might say: "from the State 'near_door,' the Verb 'open' is available, provided the player holds at least one key." Arcs can include guards — boolean expressions evaluated against current Marks (e.g., `strength >= 5` or `inventory.keys >= 1`). Arcs can consume Marks (spend a key) or produce them (gain experience). In a card game: "play an ace only if your hand contains one, and playing it removes it from your hand" is an Arc with a guard and a consumption rule.

**Mark** is a quantifiable resource or token. Health points. Ammunition. Keys. Mana. Gold. Marks are the tokens that flow through the system, consumed and produced by Arcs, accumulating on States. In a card game: cards in hand, chips in pot, score on a ledger. Marks support numeric values and structured accessor syntax (`inventory.bombs >= 1`), allowing compact representation of complex economies without changing the primitives.

Four primitives. Like four nucleotide bases, they are simple individually and unlimited in combination.

| Primitive | Role | Card Game Example |
|-----------|------|-------------------|
| **State** | Observable condition | Hand composition, pot size |
| **Verb** | Available action | Play, draw, fold, raise |
| **Arc** | Legal transition with cost | Rules governing what plays when |
| **Mark** | Quantifiable resource | Cards, chips, score |

## Composition

Primitives combine into progressively larger structures, the way codons compose into genes and genes into genomes.

An **Atom** is the smallest closed interactive loop — a single codon in the grammar. State → Verb → State. "Press button, receive reward." A locked door is one Atom: the State (door_locked), the Verb (use_key), the Arc (requires key Mark, consumes it), the resulting State (door_open). A card game's "draw a card" is one Atom: deck has cards → draw → hand increases, deck decreases.

A **Pattern** is a named, versioned cluster of Atoms — a gene in the grammar. "Locked Door" is a Pattern. "Basic Combat" is a Pattern. "Inventory Management" is a Pattern. Patterns have defined interfaces: the Marks they expect as input and produce as output. This means Patterns compose with other Patterns the way genes compose into functional systems — the output of one feeds the input of another. Patterns support extension and forking: a community publishes "Locked Door v1.0," and another community forks it with a "kick down" Verb requiring strength above a threshold. The fork is traceable. The lineage is preserved.

A **Score** is the root composition — a complete game ruleset, the full genome. "Poker Rules v1.0" is a Score. "Simple Platformer" is a Score. A roguelike is a Score composed of Patterns for movement, combat, inventory, procedural generation, and permadeath. Scores define the split between Capability Schema (what the agent can do) and Content Schema (what the world affords), preventing the conflation of rules with specific arrangements.

```yaml
version: 1.0
imports:
  - maps:open-door@1.0

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
    effects:
      - arc:
          target: door_open

  - id: kick_door
    preconditions:
      - arc:
          source: near_door
          guard: strength >= 5
```

## Schema Separation

MAPS enforces a clean division between what the rules *are* and what happens during play:

**Capability Schema** defines what the agent can do: Verbs and their preconditions. **Content Schema** defines what the world affords: States, Arcs, and available Marks. **Content Instance** is a specific arrangement: a particular level layout placing Marks in particular States. **Play State** is runtime-only — the current position during a session, excluded from the notation entirely.

The Score describes poker. A Content Instance describes the starting deal. Play State describes that a player just raised to 500. The Score and the Content Instance are preservable artifacts. The Play State is ephemeral. This separation prevents the genome from being confused with any single organism it produces.

## Integration with the Decentralized Game Standard

| Layer | Role | Connection |
|-------|------|-----------|
| **AEMS** | Persistent entities and assets | Manifestations provide the nouns (doors, swords, potions) that populate States |
| **RUNS** | Execution and data flow | Scores export to Networks of Records and Processors. A Score becomes executable when paired with RUNS implementations of its Patterns — the way a genome becomes a living system when loaded into cellular machinery |
| **WOCS** | Coordination and ecosystem | Registry for Patterns and Scores; bounties for new Pattern development; curation services |

## What the Notation Captures and What It Leaves to Others

The notation captures interactive structure: the states, actions, transitions, resources, and their composition into reusable patterns and complete rulesets. Every rule is written down. Nothing is hidden.

Timing (turn-based vs. real-time), probability and randomness, visuals and audio, and platform specifics are left to the execution layer. A Score does not prescribe whether its Verbs resolve in frames or turns, the way a genome does not prescribe the temperature of the cell or the density of the surrounding medium. RUNS Processors and Runtimes handle execution. AEMS Manifestations handle presentation. The notation handles grammar.

## Status

MAPS is a draft specification. No production implementations exist. The notation defines the minimal grammar for preservable, composable game rules. Tools for validation, diagramming, and export to RUNS Networks are open territory for anyone to build.

**MIT License** — Open for implementation, extension, critique.