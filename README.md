# Count-Min Sketch: Frequency Estimation in Large Data Streams

**A Probability-Based Study of Probabilistic Data Structures**

**Institution:** Indian Statistical Institute (ISI)  
**Course:** Probability 2

**Authors:** Daksh, Shaurya, Swapnil, Tridham, Akshay, Shinjan G

---

## Overview

How can we estimate the frequency of an item in a massive data stream without storing every observation?

This project studies the **Count-Min Sketch (CMS)**, a probabilistic data structure designed for approximate frequency estimation using significantly less memory than conventional exact frequency tables.

The project develops the mathematical foundations of Count-Min Sketch, explains its construction and operations, establishes its probabilistic error guarantees, and investigates how different properties of a data stream affect its performance.

The central idea is to replace an exact frequency table with a compact matrix of counters, multiple hash functions, and a minimum-based query operation.

## 1. The Problem

A data stream is an ordered sequence of observations or events:

\[
x_1,x_2,x_3,\ldots
\]

Examples include:

- Network packets
- Search queries
- Financial transactions
- Website clicks
- Sensor readings
- System logs

For an item \(x\), its frequency is

\[
f(x)=\#\{i:x_i=x\}.
\]

Maintaining an exact frequency table requires a separate counter for every distinct item. For massive streams, this can become prohibitively expensive.

Count-Min Sketch addresses this problem by maintaining a fixed-size summary of the stream.

## 2. Count-Min Sketch

A Count-Min Sketch consists of a matrix of counters

\[
C\in\mathbb{N}^{d\times w},
\]

where:

- \(d\) is the number of rows (depth).
- \(w\) is the number of columns (width).
- \(h_1,h_2,\ldots,h_d\) are the corresponding hash functions.

### Update Operation

When an item \(x\) arrives, update one counter in every row:

\[
C[j,h_j(x)]\leftarrow C[j,h_j(x)]+1.
\]

### Query Operation

To estimate the frequency of an item \(x\), retrieve the corresponding counter from every row and return their minimum:

\[
\boxed{
\hat f(x)=\min_{1\leq j\leq d}C[j,h_j(x)]
}
\]

Collisions may increase the estimated frequency, but under non-negative updates they cannot cause underestimation.

Therefore,

\[
\hat f(x)\geq f(x).
\]

## 3. Mathematical Analysis

The probabilistic analysis relies on the following assumptions:

1. The stream is fixed before the hash functions are randomly selected.
2. Distinct items have pairwise collision probability \(1/w\) in each row.
3. Hash functions belonging to different rows are independent.
4. All updates are non-negative.

For a fixed target item \(x\), define the collision error in row \(j\) as

\[
X_j=C[j,h_j(x)]-f(x).
\]

Equivalently,

\[
X_j=
\sum_{y\neq x}f(y)
\mathbf{1}\{h_j(y)=h_j(x)\}.
\]

Let

\[
N=\sum_y f(y)
\]

denote the total number of arrivals.

By linearity of expectation and the pairwise collision property,

\[
\mathbb E[X_j]
=
\frac{1}{w}\sum_{y\neq x}f(y)
\leq\frac{N}{w}.
\]

Using Markov's inequality,

\[
\Pr(X_j>\varepsilon N)
\leq \frac{1}{\varepsilon w}.
\]

Choosing

\[
w=\left\lceil\frac{e}{\varepsilon}\right\rceil,
\qquad
d=\left\lceil\ln\frac{1}{\delta}\right\rceil,
\]

gives the point-query guarantee:

\[
\boxed{
\Pr\left(
f(x)\leq\hat f(x)\leq f(x)+\varepsilon N
\right)\geq1-\delta
}
\]

Thus, the number of counters is

\[
\boxed{
dw=O\left(
\frac{1}{\varepsilon}\ln\frac{1}{\delta}
\right).
}
\]

The memory requirement depends on the desired accuracy and confidence rather than the number of distinct items.

## 4. Complete Worked Example

The project develops a complete example using the stream

\[
A,B,A,C,A,B,A,D,A,C,E,A,B,A,F,D,B,C,A,G.
\]

The stream contains:

- Total arrivals: \(N=20\)
- Distinct items: \(7\)
- Width: \(w=11\)
- Depth: \(d=3\)
- Total counters: \(33\)

The true frequencies are:

| Item | A | B | C | D | E | F | G |
|---|---:|---:|---:|---:|---:|---:|---:|
| True frequency | 8 | 4 | 3 | 2 | 1 | 1 | 1 |
| Estimated frequency | 8 | 4 | 3 | 2 | 1 | 1 | 4 |
| Error | 0 | 0 | 0 | 0 | 0 | 0 | 3 |

The example illustrates how collisions affect frequency estimates, particularly for rare items.

For the frequent item \(A\),

\[
\hat f(A)=\min(8,10,9)=8.
\]

For the rare item \(G\),

\[
\hat f(G)=\min(4,6,4)=4.
\]

The example demonstrates that Count-Min Sketch can estimate frequent items accurately while rare items may experience substantial overestimation.

## 5. How the Stream Affects the Sketch

The project investigates several important factors:

- **Stream length:** The additive error guarantee depends on the total number of arrivals \(N\).
- **Near-unique streams:** When most items occur only once, collision errors can dominate their true frequencies.
- **Number of distinct items:** The number of sketch counters remains fixed for chosen parameters, unlike an exact frequency table.
- **Order of arrivals:** The final sketch is independent of the order of updates.
- **Merging:** Sketches with identical dimensions and hash functions can be combined by adding their counters.
- **Forgetting old data:** Standard Count-Min Sketch does not automatically forget previous observations; sliding windows or periodic resets can be used.

## 6. Applications

Count-Min Sketch is useful in applications involving large-scale data streams, including:

- Network traffic analysis
- Approximate frequency counting
- Heavy-hitter detection
- Search query analysis
- Website traffic monitoring
- Distributed stream processing
- Large-scale event monitoring

## 7. Limitations

Despite its efficiency, Count-Min Sketch has several limitations:

- It can overestimate frequencies because of hash collisions.
- Rare items can have large relative estimation errors.
- Standard guarantees assume non-negative updates.
- Adaptive adversaries can exploit known hash functions.
- Counter overflow can invalidate the guarantees.
- It does not automatically support forgetting old observations.

For streams involving negative updates, alternative structures such as Count Sketch may be more appropriate.

## 8. Project Takeaways

This project demonstrates the connection between probabilistic analysis, hashing, and efficient data structures.

The central trade-off is:

**Less memory in exchange for controlled approximation.**

By using multiple independent hash functions and taking the minimum counter value, Count-Min Sketch provides a mathematically controlled frequency estimate while avoiding the storage requirements of exact frequency counting.

---

## References

1. T. Roughgarden and G. Valiant, *Approximate Heavy Hitters and the Count-Min Sketch*, Stanford University, CS168, 2015.
2. A. Moitra, *Problem Set 2*, MIT 6.854J/18.415J Advanced Algorithms, 2016.
3. B. Sigurleifsson, A. Anbarasu and K. Kangur, *An Overview of Count-Min Sketch and its Applications*, EasyChair Preprint No. 879, 2019.
4. N. Limaye, *Lecture 7: Count-Min and Count Sketches*, IIT Bombay, CS602, 2014.
5. Rice University, *COMP 480/580 Scribe Notes 11*, Fall 2024.

---

**Academic Project | Probability 2 | Indian Statistical Institute**
