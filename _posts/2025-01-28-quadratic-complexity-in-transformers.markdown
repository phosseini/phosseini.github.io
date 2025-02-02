---
layout: post
title: "Quadratic Complexity in Transformers"
date: 2025-01-29
permalink: /transformers/quadratic-complexity/
---

### Linguistic and Mathematical Definitions of Quadratic

Before explaining why Transformers are quadratic, let's first understand what the word "quadratic" means. From a **linguistic** perspective, the term **quadratic** comes from the Latin word **quadratus**, meaning **square**. But what does the power of two have to do with squares? Well, the name "quadratic" comes from **geometry**, not arithmetic. A square has four sides, and the area of a square with side length $x$ is:

$$x \times x = x^2$$

Since squaring a number relates to finding the area of a square, we use the term *quadratic* when referring to $x^2$.

Mathematically, a function is quadratic if its growth rate is proportional to the square of the input size:

$$y = ax^2 + bx + c$$

where $x$ is the input, and $a$, $b$, and $c$ are constants. For large $x$, the $x^2$ term dominates, making the function quadratic. Now, let's see why Transformers are quadratic.

### Why Are Transformers Quadratic?  

Transformers use the **self-attention** mechanism, which determines the importance of each token relative to every other token. As a reminder, the self-attention formula for calculating attention scores is:

$$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{Q \cdot K^T}{\sqrt{d_k}}\right)V$$  

Where:  

- $Q$ is the query matrix  
- $K$ is the key matrix  
- $V$ is the value matrix  
- $d_k$ is the dimension of the keys  
- $\sqrt{d_k}$ is the scaling factor  

For a sequence of $n$ tokens, self-attention calculates attention scores between **all token** pairs. This requires:

- **Dot-product computation** between every query and key vector ($Q \cdot K^T$), which takes $O(n^2 d)$, where $d$ is the model's hidden dimension (i.e., the size of each token's embedding vector). Since the attention matrix has $n \times n = n^2$ elements, this dominates computation.
- **Softmax normalization** across all tokens, which takes $O(n^2)$.
- **Value matrix multiplication**, which takes $O(n^2 d)$.

As for memory complexity, the attention matrix requires $O(n^2)$ space, and each matrix is of shape $n \times d$, contributing to $O(nd)$ complexity. As a result, since both computation and memory scale quadratically with sequence length, we refer to Transformers as **quadratic in complexity**.

### Why Could Being Quadratic Be a Problem?

As we increase the input size $n$, both computation and memory grow **quadratically**. For instance, doubling the sequence length $n$ makes computation **4x slower**: 

If $T(n) = k \cdot n^2$, then:

$$T(2n) = 4 \cdot T(n)$$

Now, imagine processing long sequences (e.g., 100K tokens in large LLMs). The $O(n^2)$ scaling makes both training and inference prohibitively expensive in terms of compute and memory.

There has been an ongoing effort to address this limitation of Transformers. Some of these approaches include:

- **Sparse attention** (Longformer, BigBird, Reformer) — instead of full pairwise attention, restricting attention to local windows or sampled keys.
- **Low-rank approximations** (Linformer, Performer) — reducing dimensionality by approximating the attention matrix.
- **Memory-efficient variants** (FlashAttention, xFormers) — optimizing memory access patterns to improve efficiency.
- **State-space models (SSMs, Hyena)** — replacing attention with structured state-space representations to achieve sub-quadratic scaling.

### Code Example: Quadratic Growth  

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
