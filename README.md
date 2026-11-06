# Count-Min Sketch: Frequency Estimation in Large Data Streams

A probabilistic data structure for estimating item frequencies in massive data streams using hashing, compact counters, and mathematical error guarantees.

## Project Information

**Course:** Probability 2  
**Institution:** Indian Statistical Institute

**Authors:** Daksh, Shaurya, Swapnil, Tridham, Akshay, Shinjan G

## 1. Introduction

Modern applications generate enormous data streams. Examples include network traffic, online transactions, search queries, and social-media activity.

Storing every incoming element and maintaining its exact frequency can require substantial memory. The **Count-Min Sketch (CMS)** is a probabilistic data structure that estimates item frequencies using a small two-dimensional array of counters and multiple hash functions.

Its main objective is to provide approximate frequency queries while using significantly less memory than storing the complete frequency table.

## 2. Count-Min Sketch Data Structure

A Count-Min Sketch consists of:

- A two-dimensional counter array with depth $d$ and width $w$.
- $d$ hash functions, one for each row.
- Non-negative integer counters, initially set to zero.

The counter array is:

$$
C \in \mathbb{N}^{d \times w}
$$

Each hash function maps an incoming item to a counter position:

$$
h_i(x) \in \{0,1,\ldots,w-1\}
$$

where $i=1,2,\ldots,d$.

## 3. Update Operation

When an item $x$ arrives in the stream, its corresponding counter is incremented in every row.

For each row $i$:

$$
C[i][h_i(x)] \leftarrow C[i][h_i(x)] + 1
$$

Thus, one stream update requires $d$ counter increments.

## 4. Query Operation

To estimate the frequency of an item $x$, retrieve the corresponding counter from every row and return the minimum:

$$
\widehat{f}(x)
=
\min_{1\leq i\leq d}
C[i][h_i(x)]
$$

The minimum is used because hash collisions can cause counters to overestimate frequencies.

For streams containing only non-negative updates:

$$
\widehat{f}(x)\geq f(x)
$$

Therefore, Count-Min Sketch does not underestimate the true frequency.

## 5. Mathematical Error Guarantee

Let:

- $f(x)$ denote the true frequency of item $x$.
- $\widehat{f}(x)$ denote its estimated frequency.
- $N$ denote the total number of stream updates.
- $\epsilon$ denote the additive error parameter.
- $\delta$ denote the failure probability.

For a fixed item, under the standard Count-Min Sketch assumptions, choose:

$$
w=\left\lceil\frac{e}{\epsilon}\right\rceil
$$

and

$$
d=\left\lceil\ln\left(\frac{1}{\delta}\right)\right\rceil
$$

Then the estimate satisfies:

$$
\Pr\left(
f(x)\leq\widehat{f}(x)
\leq f(x)+\epsilon N
\right)
\geq 1-\delta
$$

This means that, with probability at least $1-\delta$, the estimated frequency exceeds the true frequency by at most $\epsilon N$.

### Space Complexity

The total number of counters is:

$$
S=wd
$$

Substituting the selected width and depth gives:

$$
S=
O\left(
\frac{1}{\epsilon}
\ln\frac{1}{\delta}
\right)
$$

### Update Complexity

Each update accesses one counter in every row:

$$
T_{\mathrm{update}}=O(d)
$$

Therefore:

$$
T_{\mathrm{update}}
=
O\left(\ln\frac{1}{\delta}\right)
$$

### Query Complexity

A query examines one counter from each row:

$$
T_{\mathrm{query}}=O(d)
$$

## 6. Worked Example

Consider the following stream of 20 arrivals:

```text
A, B, A, C, A, B, A, D, A, C,
E, A, B, A, F, D, B, C, A, G
```

The actual frequencies are:

| Item | True frequency |
|---|---:|
| A | 8 |
| B | 4 |
| C | 3 |
| D | 2 |
| E | 1 |
| F | 1 |
| G | 1 |

After processing the stream, suppose the Count-Min Sketch produces the following estimates:

| Item | True frequency | Estimated frequency |
|---|---:|---:|
| A | 8 | 8 |
| B | 4 | 4 |
| C | 3 | 3 |
| D | 2 | 2 |
| E | 1 | 1 |
| F | 1 | 1 |
| G | 1 | 4 |

The estimate for item G is larger than its actual frequency because of hash collisions.

This illustrates the central property of Count-Min Sketch: estimates can be higher than actual frequencies, but not lower, under non-negative updates.

## 7. Important Properties

1. **Sublinear memory:** The structure stores a compact counter array rather than an exact frequency table.
2. **Fast updates:** Every incoming element updates exactly one counter per row.
3. **Fast queries:** Frequency estimation requires examining one counter per row.
4. **One-sided error:** With non-negative updates, the estimated frequency is never below the true frequency.
5. **Probabilistic accuracy:** The width and depth control the additive error and failure probability.

## 8. Applications

Count-Min Sketch is useful in:

- Network traffic monitoring.
- Identifying frequently accessed web pages.
- Search-query frequency estimation.
- Database query optimization.
- Heavy-hitter detection.
- Large-scale transaction analysis.
- Real-time data-stream processing.

## 9. Limitations

- Hash collisions can cause overestimation.
- It does not directly provide exact frequencies.
- Standard Count-Min Sketch is designed for non-negative updates.
- Recovering all distinct elements is not its primary purpose.
- Accuracy depends on the selected width, depth, and hash functions.

## 10. Conclusion

Count-Min Sketch provides an efficient approach to approximate frequency estimation in large data streams. By combining multiple hash functions with a compact counter matrix, it achieves low memory usage and fast updates and queries.

Its mathematical error guarantee makes it particularly useful when exact frequency storage is impractical.

## References

1. Graham Cormode and S. Muthukrishnan, *An Improved Data Stream Summary: The Count-Min Sketch and its Applications*.
2. Count-Min Sketch research literature and course materials used for this project.

---

**Note:** The mathematical guarantees above concern a fixed queried item under the standard assumptions of non-negative updates and suitable independent hash functions.
