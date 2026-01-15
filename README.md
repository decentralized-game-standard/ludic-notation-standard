# Ludic Structures

**A Declarative Framework for Composable Game Rulesets — Conceptual Exploration, 2026-01-14**

🏠 **[Overview](https://github.com/decentralized-game-standard)** · 📦 **[AEMS](https://github.com/decentralized-game-standard/aems-standard)** · 🔧 **[GERS](https://github.com/decentralized-game-standard/gers-standard)** · ⚡ **[WOSS](https://github.com/decentralized-game-standard/woss-standard)** · ❓ **[FAQ](https://github.com/decentralized-game-standard/.github/blob/main/profile/FAQ.md)**

---

Games are defined by their rules. A core loop, win conditions, resource flows, player actions—these are the essence that makes chess chess, soccer soccer, or a MOBA a MOBA. Yet in digital games, rules are almost always embedded directly in code: hard-wired into monolithic engines, scattered across scripts, or tightly coupled to proprietary systems. This locks rulesets to specific implementations, making true portability, remixing, and longevity difficult.

Ludic Structures proposes a different approach: declarative, composable descriptions of game mechanics that stand independent of any engine or codebase. Inspired by petri nets and atomic "ludemes" (fundamental units of gameplay), it treats rules as explicit, hierarchical compositions of simple primitives. A ruleset becomes a portable "rulebook"—readable, versionable, forkable—that any compatible engine can interpret in its own way.

This is not a full protocol yet. It is an early conceptual exploration, adjacent to efforts like AEMS (persistent entities), GERS (composable engines), and WOSS (coordination). The goal is to enable genres to function as open, eternal rulesets: shared foundational mechanics that communities can reference, extend, or variant without permission or lock-in.

## The Embedded Rules Problem

Current digital games fuse rules too tightly:

- Rules are imperative code, not declarative descriptions—changing one mechanic often requires refactoring unrelated systems.
- Portability is rare: a "battle royale" ruleset from one game cannot be directly imported into another engine without full reimplementation.
- Variants and mods struggle: forking behavior means duplicating code, leading to divergence and maintenance debt.
- Preservation suffers: when an engine dies, the nuanced rules encoded in it often vanish or become unreadable.

Physical and tabletop games avoid this by separating the rulebook from the playing field. Digital games can do the same.

## Core Insight: Rules as Composable Networks

Ludic Structures models games as petri-net-like directed graphs of states and transitions, built from reusable primitives.

- **Places** represent game states or conditions (e.g., "player has resource X," "timer elapsed," "objective controlled").
- **Transitions** represent actions or rules that fire when inputs are satisfied (e.g., "spend mana → cast spell," "eliminate all opponents → win").
- **Arcs** connect places to transitions with conditions, multipliers, or probabilities.
- **Tokens** flow through the net, representing dynamic game state (resources, scores, entity counts).
- **Sub-structures** are named, reusable modules (e.g., "turn order queue," "lane pushing mechanic," "inventory management") that can be instantiated and parameterized.

Higher-level games emerge by composing these into networks: simple primitives combine into core loops, which nest into full rulesets.

The description is engine-agnostic and human-readable (YAML/JSON proposed). Interpretation happens at runtime: a GERS engine loads a structure and maps it to dedicated Processors, or implements it ad hoc.

Variants are natural forks: change a transition condition, swap a sub-structure, or add probabilistic arcs.

## Why Separate Rules Description

- **Portability** — The same "classic MOBA" structure can be interpreted by minimalist engines (quick pickup games) or high-fidelity ones (pro tournaments).
- **Remixing** — Communities fork and extend openly, like house rules in board games.
- **Discovery** — Rulesets can be published, tagged, and queried independently (e.g., on Nostr or repos).
- **Analysis** — Declarative form enables tooling: balance simulation, variant generation, formal verification.
- **Longevity** — Rules outlive any one implementation; future engines can re-interpret classics faithfully or creatively.

This complements, not competes with, existing standards:
- AEMS Manifestations can reference a Ludic Structure by ID or tag.
- GERS Processors implement the behaviors described.
- No new persistence mechanism required—structures are static documents.

## Technical Sketch

A Ludic Structure is a declarative document composing primitives.

### Primitives (Conceptual)

- **Place**: Persistent state holder (e.g., resource pool, flag).
- **Transition**: Rule that fires on input conditions, consuming/producing tokens.
- **Arc**: Input/output link with optional condition, weight, probability.
- **Module**: Named sub-graph for reuse (e.g., "victory_condition: last_team_standing").

### Example: Simplified Resource → Action → Reward

```yaml
structure: basic_farm_loop
  places:
    - gold_pool
    - worker_count
    - building_queue
  transitions:
    - farm_gold:
        inputs:
          - worker_count >= 1
        outputs:
          - gold_pool +10 per second
    - build_unit:
        inputs:
          - gold_pool >= 100
        consumes:
          - gold_pool -100
        outputs:
          - worker_count +1
  modules:
    - victory: resource_threshold(gold_pool > 10000)
```

### Example: MOBA Lane Snippet (Sub-structure)

```yaml
module: lane_pushing
  places:
    - creep_wave_timer
    - tower_health_team_a
    - tower_health_team_b
  transitions:
    - spawn_creeps:
        inputs: [creep_wave_timer elapsed]
        outputs: [creep entities +10 per team]
    - damage_tower:
        inputs: [creeps reach tower, opposing creeps < own]
        outputs: [tower_health -damage based on creep differential]
```

Full games nest these: a "classic MOBA" structure instantiates three lane_pushing modules, adds hero selection, item shop, jungle camps, and victory conditions.

Storage/publishing: plain files in repos, or (future) as Nostr events for versioning and discovery.

## Integration Vision

- An AEMS Manifestation tags or embeds a Ludic Structure ID: "this game uses ludic:moba_classic_v2.1".
- GERS engines include generic interpreters or specialized Processor packs for common structures.
- Clients query for popular/forked rulesets to bootstrap pickup games.
- WOSS bounties fund curation or new modules.

## Prototype Path

1. **Define Primitives** — Sketch 10–20 core places/transitions covering common mechanics (resources, turns, scoring, spatial control).
2. **Write Sample Structures** — Document simple games (tic-tac-toe, pong loop, basic shooter).
3. **Compose a Genre** — Build a minimal MOBA or battle royale from modules.
4. **Manual Interpretation** — Playtest by hand or in a spreadsheet simulator.
5. **Engine Bridge** — Map a structure to a simple GERS prototype for automated execution.

## Status and Scope

This is early-stage thinking—deliberately minimal and uncommitted. The petri-net inspiration feels powerful for expressiveness and composition, but alternatives (state machines, decision trees, ludeme libraries) may emerge. The focus is enabling open rulebooks as a cultural artifact, separate from artifacts (AEMS), execution (GERS), and coordination (WOSS).

Feedback welcome: are the primitives right? Should storage be Nostr-native? What mechanics are hardest to declare?

Experiment. Describe a favorite game mechanic declaratively. Fork it into a weird variant.

**MIT License** — Free to use, extend, critique.
