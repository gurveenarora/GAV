# Graph Algorithms Visualizer

A Java 21 / Maven project that implements a weighted graph data structure from
scratch and demonstrates classic graph algorithms — BFS, DFS, Dijkstra, Prim,
and Kruskal — through an interactive console UI, a random graph generator,
a descriptive statistics module, a micro-benchmark harness, and a JUnit 5
test suite.

It is designed as both a learning tool (verbose, step-by-step algorithm
output) and a small, honestly-engineered codebase: layered packages, custom
exceptions, dependency-inverted components, and tests for every public
behavior, including edge cases.

---

## Features

- **Custom `Graph` data structure** — adjacency-list based, supports both
  directed and undirected graphs, weighted edges, and clear custom
  exceptions for invalid operations.
- **Core operations** — `addNode`, `removeNode`, `addEdge`, `removeEdge`,
  `containsNode`, `containsEdge`, `getNeighbors`, `getEdges`, `edgeCount`.
- **Five algorithms**, each with verbose, step-by-step console output:
  - Breadth-First Search (BFS)
  - Depth-First Search (DFS) — both recursive and iterative
  - Dijkstra's shortest path
  - Prim's Minimum Spanning Tree
  - Kruskal's Minimum Spanning Tree
- **Random graph generation** — 10, 100, 1000 (or any) nodes; sparse or
  dense edge density; optional guaranteed connectivity; no duplicate edges.
- **Graph statistics** — node/edge counts, average degree, density,
  connected-component count, and min/max degree, printed as a neat report.
- **Benchmark harness** — times every algorithm on the same graph and
  reports execution time and approximate memory usage.
- **Interactive console menu** — build a graph by hand or generate one
  randomly, then run any algorithm against it.
- **JUnit 5 test suite** — covers every core operation and algorithm,
  including edge cases (empty graphs, single nodes, disconnected graphs,
  self-loops, missing nodes/edges).

---

## Project Structure

```
graph-algorithms-visualizer/
├── pom.xml
├── README.md
└── src/
    ├── main/java/com/graphvisualizer/
    │   ├── model/            Node, Edge — plain data types
    │   ├── graph/             Graph — adjacency-list storage & structural ops
    │   ├── algorithms/        BFS, DFS, Dijkstra, Prim, Kruskal, DisjointSet,
    │   │                      and their result types
    │   ├── generator/         GraphGenerator (interface), RandomGraphGenerator,
    │   │                      GraphDensity
    │   ├── statistics/        GraphStatistics, TraversalStrategy (interface)
    │   ├── benchmark/         BenchmarkRunner, BenchmarkResult
    │   ├── util/              GraphUtils, and util.exceptions.* (GraphException
    │   │                      hierarchy: DuplicateNodeException,
    │   │                      DuplicateEdgeException, NodeNotFoundException,
    │   │                      EdgeNotFoundException)
    │   └── ui/                Main (entry point), ConsoleMenu (interactive loop)
    └── test/java/com/graphvisualizer/
        ├── graph/              GraphTest
        ├── algorithms/         BFSTest, DFSTest, DijkstraTest, PrimTest, KruskalTest
        ├── generator/          RandomGraphGeneratorTest
        └── statistics/         GraphStatisticsTest
```

**Design notes:**

- `graph` only knows how to store and query structure — it has no notion of
  "visited" or "shortest distance", so every algorithm operates on the same
  `Graph` instance without that class growing a method per algorithm
  (Single Responsibility).
- `GraphGenerator` and `TraversalStrategy` are interfaces that their
  concrete implementations (`RandomGraphGenerator`, `BFS::run`) satisfy,
  so `GraphStatistics` and any future caller depend on an abstraction, not
  a concrete class (Dependency Inversion) — this is also what makes
  `GraphStatistics` testable with a fake traversal (see
  `GraphStatisticsTest.statistics_usesInjectedTraversalStrategy`).
- A single `GraphException` hierarchy means callers can catch precise
  failure types (`NodeNotFoundException`, `DuplicateEdgeException`, etc.)
  instead of generic runtime exceptions.

---

## Algorithms Implemented

| Algorithm | Type | File |
|---|---|---|
| Breadth-First Search | Traversal | `algorithms/BFS.java` |
| Depth-First Search (recursive) | Traversal | `algorithms/DFS.java` |
| Depth-First Search (iterative) | Traversal | `algorithms/DFS.java` |
| Dijkstra's Algorithm | Single-source shortest path | `algorithms/Dijkstra.java` |
| Prim's Algorithm | Minimum Spanning Tree | `algorithms/Prim.java` |
| Kruskal's Algorithm | Minimum Spanning Tree | `algorithms/Kruskal.java` + `algorithms/DisjointSet.java` |

---

## Screenshots

> _Add screenshots of the console menu, a sample BFS/DFS run, and a
> generated graph's statistics report here._

```
docs/screenshots/menu.png
docs/screenshots/bfs-run.png
docs/screenshots/statistics-report.png
```

---

## How to Run

**Requirements:** JDK 21+, Maven 3.9+

```bash
# Clone and enter the project
git clone <your-repo-url>
cd graph-algorithms-visualizer

# Compile
mvn compile

# Run the interactive console app
mvn exec:java

# Run the full JUnit 5 test suite
mvn test
```

If you prefer not to use the `exec-maven-plugin` shortcut, you can run the
compiled class directly:

```bash
mvn package
java -cp target/classes com.graphvisualizer.ui.Main
```

---

## Example Input

A short interactive session — building a 4-node graph, then generating a
random one:

```
=== Graph Algorithms Visualizer ===
...
14. Generate Random Graph (replaces current graph)
0. Exit
Choose an option: 14

Enter number of nodes (e.g. 10, 100, 1000): 10
Density - sparse or dense? (s/d): s
Guarantee the graph is connected? (y/n): y
```

## Example Output

```
Generated a SPARSE graph with 10 node(s) and 15 edge(s).
--------------------------------------
Graph Statistics
--------------------------------------
Nodes:                  10
Edges:                  15
Average degree:         3.0000
Density:                0.333333
Connected components:   1
Maximum degree:         5
Minimum degree:         2
--------------------------------------
```

Running Dijkstra from node `0` on that graph (menu option 9) prints, for
each step, the current node, the priority queue contents, the distance
table, and the parent table, finishing with:

```
Shortest Path: 0 -> 3 -> 7
Total Cost: 42.17
```

---

## Complexity Table

| Algorithm | Time Complexity | Space Complexity | Notes |
|---|---|---|---|
| BFS | O(V + E) | O(V) | V = nodes, E = edges |
| DFS (recursive) | O(V + E) | O(V) (call stack) | Recursion depth = longest path from start |
| DFS (iterative) | O(V + E) | O(V) (explicit stack) | Same visited set as recursive, order may differ |
| Dijkstra | O((V + E) log V) | O(V) | Binary-heap priority queue; requires non-negative weights |
| Prim's MST | O(E log V) | O(V + E) | Binary-heap priority queue over candidate edges |
| Kruskal's MST | O(E log E) | O(V + E) | Dominated by sorting edges; Union-Find is ~O(α(V)) per op |
| Random Graph Generation | O(V + E) expected | O(V + E) | E bounded by density target; rejection sampling for uniqueness |

---

## Future Improvements

- Add a graphical (JavaFX/Swing or web) visualizer to animate traversals
  and MST construction, instead of console text output.
- Support negative-weight shortest paths via Bellman-Ford, and cycle
  detection for directed graphs.
- Make `GraphStatistics`'s connected-component count fully correct for
  directed graphs (true weak-connectivity, not just forward-reachability).
- Add a JMH-based benchmark suite to replace the current lightweight
  `System.nanoTime()`/`Runtime` measurements with statistically rigorous ones.
- Persist and load graphs from files (adjacency list / edge list / JSON).
- Add a `GraphBuilder` fluent API as an alternative to manual `addNode`/`addEdge` calls.

## Technologies Used

- **Java 21**
- **Maven** (`maven-compiler-plugin`, `maven-surefire-plugin`, `exec-maven-plugin`)
- **JUnit 5** (Jupiter API + Params) for unit testing

## License

This project is licensed under the [MIT License](LICENSE). Feel free to use,
modify, and extend it for learning, coursework, or portfolio purposes.
