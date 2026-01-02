# SAC_SPARK_DIJKSTRA
Dijkstra simulator for node weight pathing using Spark

# Distributed Dijkstra Algorithm with PySpark

This repository contains a Python implementation of **Dijkstra's Shortest Path Algorithm** utilizing **Apache Spark RDDs**. It is designed to calculate the shortest path from a source node to all reachable nodes in a directed, weighted graph using distributed computing principles.

## Project Overview

Dijkstra's algorithm is traditionally a sequential process. This implementation adapts the logic for a distributed environment by using Spark's parallel processing capabilities to evaluate and update node distances across a cluster.

### Key Features
* **Graph Flexibility**: Supports both manually defined example graphs and randomly generated directed graphs via `NetworkX`.
* **Distributed State Management**: Each node's state (neighbors, distance, visited status, and path) is stored in a Spark RDD.
* **Parallel Relaxation**: In each iteration, Spark updates the distances of all neighbors of the current pivot node simultaneously.
* **Stability**: Incorporates RDD caching (`.cache()`) and materialization (`.count()`) to manage the Spark execution lineage and prevent memory failures during long iterative processes.

---

## Technical Structure

The graph data is processed in the following RDD format:
`('Node_ID', ([(Neighbor, Weight)], Distance, Visited, [Path_List]))`

### Main Components
 **Graph Generators**: 
    * `example_graph()`: A fixed test case with nodes A through K.
    * `generate_random_graph()`: Creates a random directed graph with customizable density and weight ranges.
    
 **Preprocessing**: `data_preprocesing()` converts standard NetworkX graphs into the specific RDD structure required for the solver.
 
 **Core Solver**: `dijkstra_spark()` implements the iterative logic. It identifies the unvisited node with the global minimum distance and expands it.
 
 **Reporting**: Includes `print_initial_state()` and `print_final_results()` for clean, formatted English output in the console.

---

## How to Run

### Prerequisites
* Python 3.x
* PySpark
* NetworkX

## Example Result Output

### Initial State
| NODE | INIT DIST | VISITED | NEIGHBORS (DESTINATION, WEIGHT) |
| :--- | :--- | :--- | :--- |
| A | 0.0 | False | (B, 3.0), (C, 10.0), (E, 4.0) |
| B | Inf | False | (C, 2.0), (D, 8.0), (F, 7.0) |

### Final Results
| NODE | DISTANCE | OPTIMAL PATH |
| :--- | :--- | :--- |
| C | 5.0 | B -> C |
| D | 10.0 | B -> C -> D |

## Important Note on Spark Lineage
In iterative algorithms like Dijkstra, Spark builds a massive execution plan (lineage). To avoid a `StackOverflowError`, this code explicitly calls `.cache()` and `.count()` in every loop to truncate the lineage and ensure the intermediate RDDs are stored in memory.
