---
layout: post
title: "Understanding Quadratic Complexity in Transformers"
date: 2025-01-29
permalink: /transformers/quadratic-complexity/
---

### Linguistic and Mathematical Definitions of `Quadratic`

The term **"quadratic"** comes from the Latin word **"quadratus"**, meaning **square**.

#### Why "Quad" (Four) Instead of "Two"?
- The name comes from **geometry**, not arithmetic.
- A **square** has **four sides**.
- The **area** of a square with side length $ x $ is:
  $$
  x \times x = x^2
  $$
- Since squaring a number relates to finding the **area of a square**, the term "quadratic" was used.

A function is **quadratic** if its growth rate is proportional to the square of the input size:

$$ y = ax^2 + bx + c $$

where:
- $ x $ is the input,
- $ a $, $ b $, and $ c $ are constants.

For large $ x $, the $ x^2 $ term dominates, making the function **quadratic**.

### Quadratic Growth
If a process has **quadratic complexity** $O(n^2)$, doubling the input size $n$ makes the output (or runtime) grow approximately **4 times**. If $T(n) = k. n ^ 2$, then if you calculate the $T(2n)$, you'll see $T(2n) = 4 * T(n)$

#### Example Calculation
For $f(n) = n^2$:
- $ f(2) = 4 $
- $ f(4) = 16 $
- $ f(8) = 64 $

## Why Are Transformers Quadratic?

### Self-Attention Mechanism
Transformers use **self-attention**, which determines the importance of each token relative to every other token.

For a sequence of $ n $ tokens, self-attention:
1. Computes **queries ($ Q $)**, **keys ($ K $)**, and **values ($ V $)**.
2. Calculates attention scores between all token pairs:
   $$ \text{score}(i, j) = Q_i \cdot K_j^T $$
3. Applies the scores to the **values ($ V $)**.

### Computational Cost
- The attention matrix has **$ n \times n $** elements.
- This requires **quadratic computation**: 
  $$ O(n^2) $$
- Doubling the sequence length $ n $ makes computation **4x slower**.

## Code Example: Quadratic Growth

The following code demonstrates **quadratic complexity** in self-attention.

```python
import numpy as np
import time
import matplotlib.pyplot as plt

# Function to measure self-attention runtime
def measure_runtime(sequence_length, embed_dim=64, runs=5):
    times = []
    for _ in range(runs):
        Q = np.random.rand(sequence_length, embed_dim)
        K = np.random.rand(sequence_length, embed_dim)

        start = time.time()
        np.dot(Q, K.T)  # Self-attention matrix computation
        end = time.time()
        
        times.append(end - start)
    
    return np.mean(times)  # Return average time

# Test with increasing sequence lengths
seq_lengths = [10, 100, 1000]
times = [measure_runtime(n) for n in seq_lengths]

# Plot results
plt.plot(seq_lengths, times, marker='o', linestyle='-')
plt.xlabel("Sequence Length (n)")
plt.ylabel("Time (seconds)")
plt.title("Quadratic Complexity of Self-Attention")
plt.grid()
plt.show()
```