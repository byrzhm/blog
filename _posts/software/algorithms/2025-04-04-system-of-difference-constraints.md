---
title: "Tutorial: System of Difference Constraints"
date: 2025-01-04 15:32:00 +0800
categories: [Software, Algorithms]
tags: [algorithms, sdc]     # TAG names should always be lowercase
pin: false
math: true
mermaid: false
---

Sure! Here's a detailed tutorial on **System of Difference Constraints** – a powerful concept in linear programming and graph theory, often used in scheduling, verification, and optimization problems.

---

# 📘 Tutorial: System of Difference Constraints

---

## 🔍 What is a System of Difference Constraints?

A **system of difference constraints** is a special type of linear inequality system where each inequality is of the form:

$$
x_i - x_j \leq c
$$

Here,  
- $ x_i $, $ x_j $ are variables  
- $ c $ is a constant

These constraints express that the *difference* between two variables must be at most some constant. Such systems are useful in scenarios where relative timing or order matters (e.g., task scheduling).

---

## 🧠 Real-World Examples

- **Task Scheduling:** Task A must finish at least 3 hours before Task B starts.  
  $$
  \text{start}_B - \text{end}_A \geq 3 \Rightarrow \text{end}_A - \text{start}_B \leq -3
  $$

- **Timing Constraints in Circuits:** One signal must arrive no later than 5 ns after another.

---

## 📐 Standard Form

A system of $ m $ difference constraints over $ n $ variables looks like:

$$
x_i - x_j \leq c_k \quad \text{for } k = 1, 2, ..., m
$$

You can represent this as a list of inequalities.

---

## 🔄 Graph Representation

We can convert the system into a **weighted directed graph**, where:

- Each variable $ x_i $ is a **node**
- Each constraint $ x_i - x_j \leq c $ becomes an edge from $ x_j $ to $ x_i $ with weight $ c $

This is because:
$$
x_i \leq x_j + c \Rightarrow \text{edge from } x_j \to x_i \text{ with weight } c
$$

---

## 💡 Why Use Graphs?

Once represented as a graph, solving the system reduces to checking for **negative-weight cycles**.

---

## 🛠️ Solving the System (Using Bellman-Ford)

To determine if a system of difference constraints is satisfiable:

1. **Add a source node $ s $** connected to every variable $ x_i $ with zero-weight edges.
2. **Run Bellman-Ford** from $ s $ to detect negative-weight cycles.
   - If none exist, the system is feasible.
   - The shortest-path distances give a solution.

### ✅ Example

Given the constraints:
- $ x_2 - x_1 \leq 4 $
- $ x_3 - x_2 \leq -1 $
- $ x_1 - x_3 \leq -2 $

Create a graph:
- Edge from $ x_1 \to x_2 $ with weight 4
- Edge from $ x_2 \to x_3 $ with weight -1
- Edge from $ x_3 \to x_1 $ with weight -2

Add dummy node $ s $ with 0-weight edges to all variables. Run Bellman-Ford from $ s $.

If there's a negative cycle → system is **infeasible**.

---

## 🧮 Complexity

- **Bellman-Ford Time Complexity:** $ O(VE) $
- Works well for sparse graphs.

---

## 🧰 Applications

- **Scheduling with Precedence Constraints**
- **Program Verification** (timing/ordering)
- **Network Timing Analysis**
- **Temporal Reasoning in AI**

---

## 🧾 Summary

| Concept                         | Description                                  |
|-------------------------------|----------------------------------------------|
| Constraint Form                | $ x_i - x_j \leq c $                        |
| Representation                 | Directed weighted graph                      |
| Solving Method                 | Bellman-Ford (detect negative cycles)        |
| Feasibility Check              | No negative-weight cycle                     |
| Result (if feasible)          | Shortest paths give a valid assignment       |
