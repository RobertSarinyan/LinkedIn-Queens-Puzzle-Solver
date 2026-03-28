# LinkedIn Queens – CSP and Local Search Solvers

This project implements and analyzes solvers for a Queens-like puzzle that we call **LinkedIn Queens**.

The board is divided into regions and we must place one queen in each row so that:
- no two queens share the same column;
- no two queens occupy cells in the same region;
- no two queens are adjacent horizontally, vertically, or diagonally.

We treat this both as a **Constraint Satisfaction Problem (CSP)** and as a **local search** optimization problem, and we compare several algorithms on 8×8 and 12×12 boards.

---

## Repository contents

- `AI_LinkedIn_Queens_Implementation_and_Analysis.ipynb`  
  Jupyter notebook with all implementations, experiments, and plots.

- `AI Project Report`  
  Final written report describing the problem, methods, experiments, and conclusions.

---

## Main features

### 1. Grid generation

We generate **always-solvable** boards:

1. Start from a valid classical N-Queens solution (one queen per row and column, no diagonal attacks).
2. Assign each queen a unique region ID.
3. Grow regions outward so that each region remains connected.
4. If region growth fails, restart until a valid partition is found.

We also define a **grid complexity score** based on:
- total conflicts in a random one-queen-per-row placement;
- region adjacency density (how “tight” the regions are);
- variance of region sizes.

### 2. Exact CSP solvers

- **Simple backtracking**  
  Depth-first search over rows. At each row we try all columns that are consistent with:
  - column, region, and adjacency constraints.

- **MAC (Forward Checking + AC-3)**  
  Backtracking with domains for each row and:
  - forward checking after each assignment;
  - AC-3 to maintain arc consistency between rows.

These solvers always find a solution when it exists; MAC prunes more but is computationally heavier.

### 3. Local search solvers

State representation: an array of length `n`, where entry `r` is the column of the queen in row `r` (a complete configuration).

- **Min-Conflicts + Restarts**
  - Start from a random one-queen-per-row placement.
  - Repeatedly:
    1. find all conflicting rows;
    2. pick one of them at random;
    3. try all columns in that row and compute the conflicts;
    4. move the queen to a column with minimum conflicts (random tie-break).
  - If no solution is found within a step limit, restart from a new random configuration.

- **Simulated Annealing**
  - At each step propose a child state by moving one queen in a random row to a different random column.
  - Let $\triangle$ be the change in total conflicts:
    - if $\triangle \le 0$, always accept;
    - if $\triangle \gt 0$, accept with probability $e^{-\triangle / T}$.
  - After each move, update $T\leftarrow\alpha T$ (cooling rate $\alpha$).
  - We tune $T_{0}$ and $\alpha$ and visualize success rates in a 3×3 heatmap.
---

## Experiments

All experiments are implemented inside the notebook:

1. **Accuracy and runtime on random grids**  
   Compare success rate and average runtime of:
   - Backtracking
   - MAC (FC + AC-3)
   - Min-Conflicts
   - Min-Conflicts + Restarts
   - Simulated Annealing  
   on 8×8 and 12×12 boards.

2. **SA parameter heatmap**  
   Fixed set of 12×12 grids, vary:
   - initial temperature $T_{0} \in$ {1,5,20}
   - cooling rate $\alpha \in$ {0.99, 0.995, 0.999}  
   Plot success rate as a heatmap.

3. **Restart sensitivity for Min-Conflicts**  
   Fixed grids, vary number of restarts  
   (1, 3, 5, 10, 20) and measure success rates.

4. **Runtime distributions**  
   For 12×12 grids, collect timings of successful runs and show:
   - histograms
   - scatter plots
   - boxplots  
   for backtracking, Min-Conflicts, Min-Conflicts + Restarts, and SA.

5. **Grid complexity vs solver behavior**  
   For many 12×12 grids:
   - compute the complexity score;
   - analyze how runtime and success probability change with complexity.

---

## Setup and usage

1. **Requirements**

   - Python 3.10+ (adjust if needed)
   - Typical libraries: `numpy`, `matplotlib`, `tqdm` (and any others used in the notebook).

   Install with e.g.:

   ```bash
   pip install numpy matplotlib tqdm
