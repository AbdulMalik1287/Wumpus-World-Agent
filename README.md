# Wumpus World Agent using Propositional Logic

Artificial Intelligence group coding assignment.

An agent explores a grid cave containing one Wumpus, some pits and the gold. In every cell
it senses Stench, Breeze or Glitter, writes those percepts into a propositional-logic
knowledge base, and proves which cells are safe using DPLL with proof by refutation. It only
ever steps into cells that are proven safe, grabs the gold and walks back to the start.
Everything is written with plain functions (no classes).

## Group members

| Name | Roll number |
|---|---|
| Abdul Malik | BT24CSA046 |
| Ashwin Rathore | BT24CSA045 |
| Yash Khedekar | BT24CSA049 |
| Rajat | BT24CSA044 |
| Lakshya Rajdev | BT24CSA059 |

## Running it

You need Python 3, Jupyter and Matplotlib.

```
pip install notebook matplotlib
jupyter notebook Wumpus_World_Agent.ipynb
```

Run all cells. The notebook is saved with its outputs, so the results are visible on GitHub
without running it.

## How the work is split

Every cell in the notebook is tagged with its part (`# [P1]` ... `# [P4]`).

| Part | Covers |
|---|---|
| P1 | World grid, percept generator, propositional symbols and connectives, truth tables |
| P2 | CNF converter, DPLL inference engine (entailment), knowledge base and world rules |
| P3 | Reasoner (tell percepts, ask safety), planner (BFS over safe cells), agent loop |
| P4 | Demos, path display and plot, step-by-step mode, random-world testing |

## How it works

**World and percepts.** The world is a dict holding the grid size, the Wumpus cell, the pit
cells and the gold cell. Cells are `(x, y)` with `(1, 1)` the bottom-left start. A cell gives
Stench if a neighbour has the Wumpus, Breeze if a neighbour has a pit, and Glitter if the
gold is in it. The agent only ever sees these three values.

**Logic.** Symbols are strings such as `P(1,2)` (pit), `W(1,2)` (Wumpus), `B(1,2)` (breeze)
and `S(1,2)` (stench). Sentences are tuples such as `("<=>", a, b)`, built with `Not`, `And`,
`Or`, `Implies` and `Iff`, and can be evaluated and printed as truth tables.

**CNF.** Sentences are converted to clauses by removing `<=>` and `=>`, pushing negations
inwards (De Morgan) and distributing OR over AND.

**Inference.** `KB |= q` is checked by adding `~q` to the knowledge base and running DPLL.
If the clauses become unsatisfiable, `q` is proven. DPLL uses unit propagation and branches
on a literal only when no unit clause is left.

**Knowledge base.** It starts with the rules of the world for every cell:
`B(x,y) <=> pit in some neighbour`, `S(x,y) <=> Wumpus in some neighbour`, the start cell
is safe, and there is exactly one Wumpus.

**Reasoner and planner.** After each move the agent tells the KB its percepts and asks, for
every cell, whether it is provably safe, provably a pit or provably the Wumpus. A cell that
is not proven safe is never entered. Breadth-first search over safe cells picks the nearest
unvisited safe cell and the shortest route home.

**Scoring.** -1 per move, +1000 for returning with the gold, -1000 for dying.

## Results

| World | Result | Moves | Score |
|---|---|---|---|
| 4x4 demo (Wumpus (3,1), pits (1,3) (4,3) (3,4), gold (3,2)) | WON | 8 | 992 |
| 4x4 trap (Wumpus (2,1)) | STUCK, refuses to gamble | 0 | 0 |
| 5x5 (Wumpus (2,4), pits (4,1) (3,3) (5,4), gold (5,5)) | WON | 24 | 976 |

Over 300 random 4x4 worlds (seed 7, 20% pit chance) the agent won 103, got stuck in 197 and
died in 0. Zero deaths shows the reasoning is sound: it never enters a cell it has not
proven safe. It gets stuck only when the gold cannot be reached without taking a risk.
