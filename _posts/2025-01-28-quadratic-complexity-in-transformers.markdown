---
layout: post
title: "Quadratic Complexity in Transformers"
date: 2025-01-29
permalink: /transformers/quadratic-complexity/
---

### Linguistic and Mathematical Definitions of Quadratic

Before explaining why Transformers are quadratic, let's first understand what quadratic means. From the liguistics
perpective, the term **quadratic** comes from the Latin word **quadratus**, meaning **square**. But what does the power
two have to do with squares?! Well, the name quadratic comes from geometry, not arithmetic. A square has four sides and
the area of a square with side length $x$ is:  $x \times x = x^2$. Since squaring a number relates to finding the area
of a square, we use the term *quadratic* when referring to $x^2$.

Mathematically, a function is quadratic if its growth rate is proportional to the square of the input size:

$$y = ax^2 + bx + c$$

where $x$ is the input, and $a$, $b$, and $c$ are constants. For large $x$, the $x^2$ term dominates, making the
function quadratic.

### Why Are Transformers Quadratic?

Transformers use **self-attention** mechanism, which determines the importance of each token relative to every other
token. As a reminder, the self-attention formula for calculating attention scores is:

$$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{Q \cdot K^T}{\sqrt{d_k}}\right)V$$

Where:

$Q$ is the query matrix  
$K$ is the key matrix  
$V$ is the value matrix  
$d_k$ is the dimension of the keys  
$\sqrt{d_k}$ is the scaling factor

So, for a sequence of $n$ tokens, self-attention calculates attention scores between **all token** pairs ($Q \cdot K^T$)
. As a result, the attention matrix has $n \times n = n^2$ elements and computationally this requires **quadratic
computation**: $O(n^2)$. And that is why we say Transformer is a quadratic architecture. As we change the input size $n$
to larger and larger numbers, we will see how that is going to increase computation quadratically. For instance,
doubling the sequence length $n$ makes computation *4x slower*: If $T(n) = k \cdot n ^ 2$, then $T(2n) = 4 * T(n)$

### Code Example: Quadratic Growth

The following code demonstrates **quadratic complexity** in self-attention.

```python  
import numpy as np  
import time  
import matplotlib.pyplot as plt  
  
# Function to measure self-attention runtime  
def measure_runtime(sequence_length, embed_dim=64, runs=5):  
 times = [] for _ in range(runs): Q = np.random.rand(sequence_length, embed_dim) K = np.random.rand(sequence_length, embed_dim)  
 start = time.time() np.dot(Q, K.T)  # Self-attention matrix computation end = time.time()         times.append(end - start)  
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