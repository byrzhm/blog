---
title: Balls into bins
date: 2025-03-07 19:38:00 +0800
categories: [Math, Probability]
tags: [balls-into-bins]     # TAG names should always be lowercase
pin: false
math: true
mermaid: false
---

## Introduction
The **Balls-Into-Bins** problem is a fundamental problem in probability theory, computer science, and load balancing. It models how objects (balls) are distributed among containers (bins) randomly. Understanding this problem is crucial for designing efficient hashing schemes, load-balancing algorithms, and randomized algorithms.

## Problem Statement
Given **n** balls and **m** bins, each ball is placed into a bin independently and uniformly at random. We seek to analyze various properties of this random allocation, such as:
- The expected number of balls in a given bin.
- The maximum number of balls in any bin.
- The probability that a bin is empty.
- The distribution of the number of balls across bins.

## Basic Analysis
### Expected Number of Balls in a Bin
Since each of the **n** balls is placed into one of the **m** bins uniformly at random, the probability that a specific ball lands in a given bin is **1/m**. By linearity of expectation, the expected number of balls in a single bin is:

$$
E[X] = n \cdot \frac{1}{m} = \frac{n}{m}
$$

where **X** is the random variable denoting the number of balls in a fixed bin.

### Maximum Load (Max Balls in Any Bin)
A crucial metric in load balancing is the **maximum load**, i.e., the maximum number of balls in any bin. The expected maximum load depends on the relationship between **n** and **m**:
- If **n = m**, the maximum load is about **O(log n / log log n)** with high probability.

### Probability of an Empty Bin
The probability that a specific bin remains empty after **n** balls have been placed is given by:

$$
P(\text{empty bin}) = \left( 1 - \frac{1}{m} \right)^n.
$$

For large **n** and **m**, using the approximation $ (1 - x)^n \approx e^{-nx} $, this probability is approximately:

$$
P(\text{empty bin}) \approx e^{-n/m}.
$$

## Improvements: Two-Choice and Power-of-Two Choices
A simple yet powerful improvement to the uniform random allocation is the **two-choice paradigm**:
1. Instead of placing a ball into a random bin, choose **two** bins uniformly at random.
2. Place the ball into the less occupied of the two bins (breaking ties arbitrarily).

This technique significantly reduces the maximum load from **O(log n / log log n)** to **O(log log n)** with high probability. This is known as the **Power-of-Two Choices** method and is widely used in load balancing and distributed systems.

## Applications
The Balls-Into-Bins problem has several practical applications:
- **Hashing**: Hash tables with chaining use this model to analyze collision probabilities.
- **Load Balancing**: Distributing tasks among servers in a distributed system.
- **Parallel Computing**: Assigning work items to processors efficiently.
- **Networking**: Packet routing and congestion control.

## Conclusion
The Balls-Into-Bins problem is a fundamental theoretical problem with widespread practical applications. Understanding its properties allows for designing efficient algorithms and improving system performance. The two-choice paradigm shows that a small modification in the allocation strategy can lead to significant performance gains.

