# Capstone Report: Mini-Courier Planner

## 1. Introduction
The Mini-Courier Planner is a Python prototype that combines sorting, dynamic programming, and graph algorithms to model parcel selection and courier delivery planning.

## 2. Problem definition
A courier has a limited vehicle capacity. Each parcel has an ID, priority, deadline, weight and value. Delivery points are connected by weighted roads. The system must prioritize parcels, choose a high-value feasible parcel set, find shortest paths from the depot, and estimate a tour visiting all delivery points.

## 3. Data design
### Parcel fields
- `id`: unique parcel identifier.
- `priority`: integer from 1 to 5; larger means more important.
- `deadline`: smaller integer means earlier delivery deadline.
- `weight`: parcel weight in kg.
- `value`: business value/benefit used by the knapsack objective.

### Graph representation
The road network uses an adjacency list. For example, `Depot: {A: 4, B: 7}` means Depot is connected to A by a road of 4 km and to B by a road of 7 km. The supplied sample graph is undirected and stores reverse edges.

## 4. Algorithms and rationale
### 4.1 Merge Sort
Parcels are ordered by descending priority. When priorities tie, the earlier deadline is placed first. Merge Sort was selected because its worst-case running time is O(n log n), independent of the original input order.

### 4.2 0/1 Knapsack using Dynamic Programming
The DP state `dp[i][c]` represents the maximum total value obtainable using the first `i` parcels with capacity `c`. For each parcel, the algorithm compares excluding it with including it (if its weight fits). Each parcel can be selected at most once. Time complexity is O(nW), and the full table uses O(nW) space.

### 4.3 Dijkstra's Shortest Path
Dijkstra uses an adjacency list and a min-priority queue. It calculates shortest distances from the depot to every reachable location. All edge weights must be non-negative. With a binary heap, complexity is O((V+E) log V).

### 4.4 Nearest Neighbor TSP Approximation
Starting at the depot, the algorithm repeatedly chooses the closest unvisited neighbor. It returns to the depot if a return edge exists. The heuristic is fast and easy to understand, but it does not guarantee the globally shortest possible tour. The implementation scans unvisited neighbors at each step, giving O(V²) time.

## 5. Integrated workflow
1. Read parcel and graph JSON files.
2. Sort parcels by priority and deadline.
3. Select parcels within the 15 kg vehicle capacity using 0/1 Knapsack.
4. Calculate shortest paths from the depot using Dijkstra.
5. Build an approximate delivery tour using Nearest Neighbor.
6. Display results and execution time of each component.

## 6. Testing and Performance Analysis

The application was tested using different input sizes to evaluate the execution time of all four algorithms.

The performance testing script measures:
- Merge Sort
- 0/1 Knapsack using Dynamic Programming
- Dijkstra's Shortest Path
- Nearest Neighbor TSP Approximation

The input sizes used in the experiment are 10, 20, 40, 80, and 120.

Each algorithm was executed five times for every input size. The median execution time was recorded using Python's `time.perf_counter()` function.

### 6.1 Performance Results

| Input Size | Merge Sort (seconds) | Knapsack DP (seconds) | Dijkstra (seconds) | Nearest Neighbor TSP (seconds) |
|---:|---:|---:|---:|---:|
| 10 | 0.00001150 | 0.00013430 | 0.00001230 | 0.00001160 |
| 20 | 0.00002920 | 0.00025890 | 0.00003770 | 0.00002860 |
| 40 | 0.00006990 | 0.00054960 | 0.00012520 | 0.00011020 |
| 80 | 0.00021340 | 0.00136590 | 0.00045340 | 0.00041390 |
| 120 | 0.00031300 | 0.00180020 | 0.00107500 | 0.00113030 |

### 6.2 Performance Observations

1. Merge Sort shows increasing execution time as the number of parcels increases.
2. Knapsack DP takes comparatively more time because its execution depends on both the number of parcels and the capacity.
3. Dijkstra's execution time increases as the number of graph vertices and edges increases.
4. Nearest Neighbor TSP also takes more time for larger graphs because it repeatedly searches for the nearest unvisited location.
5. The measured execution times are very small and may vary depending on the computer and system load.

The results are stored in `results/performance_results.csv`, and the runtime comparison graph is saved in `results/runtime_comparison.png`.

The experiment demonstrates the practical runtime behaviour of the four algorithms for the tested input sizes.
## 7. Correctness checks
- Merge Sort output is ordered by priority descending and deadline ascending.
- Knapsack never selects a parcel more than once and never exceeds the capacity.
- Dijkstra returns shortest distances for non-negative road weights.
- Nearest Neighbor visits every graph node once before returning to the depot when possible.
- Unit tests can be executed with `python -m unittest discover -s tests -v`.

## 8. Limitations and future improvements
- The current knapsack objective maximizes parcel value; priority and deadline are used for sorting but are not constraints in the DP objective.
- Nearest Neighbor is approximate and can produce a longer tour than the optimal TSP route.
- The sample road graph is small and static.
- Future work could add deadline feasibility, one-way roads, multiple vehicles, live traffic, larger benchmark datasets, and stronger TSP heuristics such as 2-opt.

## 9. Conclusion
The project integrates four algorithmic techniques into one courier-planning workflow. It demonstrates how sorting prioritizes work, dynamic programming optimizes capacity usage, Dijkstra finds shortest paths, and a TSP heuristic estimates a practical multi-stop route. The project also records runtime to support basic performance comparison.

## 10. Execution
From the project folder, run:
```bash
python main.py
```
Run unit tests:
```bash
python -m unittest discover -s tests -v
```
