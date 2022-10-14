---
title: "Curve Finance Part 1: The StableSwap Invariant"
categories:
  - Blog
excerpt: "This post is the first in a multipart series exploring the mathematics behind Curve Finance stable pools. We will begin by introducing the StableSwap invariant and discuss some of the mathematics behind its construction."
---

This post is the first in a multipart series exploring the mathematics behind [Curve Finance](https://curve.fi/) stable pools. We will begin by introducing the StableSwap invariant and discuss some of the mathematics behind its construction.

$$
A n^n \sum x_i + D = A D n^n + \frac{D^{n+1}}{n^n \prod x_i}
$$

Curve introduced the StableSwap invariant in their initial [whitepaper](https://curve.fi/files/stableswap-paper.pdf), published on Nov. 10, 2019. The StableSwap invariant is the equation that defines the dynamics of Curve stable pools. The success of this invariant has allowed Curve to grow to become a dominant DEXs for swapping stablecoins.

To understand the StableSwap invariant, we must first take a look at two other invariants, the constant sum, and constant product invariants.

## Constant Sum Invariant
The first invariant to consider is the constant sum invariant. Mathematically, for a pool with $n$ coins, where $x_i$ represents the supply of the $i$th coin, the invariant is given by

$$
\sum x_i = D
$$

where $D$ is a constant. When swaps occur in a pool, $D$ remains constant. Only when liquidity is added or removed from the pool does $D$ change.

The consequence of this invariant is that swapping $\delta_j$ of the $j$th coin will always result in receiving $\delta_j$ of the $k$th coin.

As an example, consider a pool containing:

- 1,000,000 USDC
- 1,000,000 USDT
- 1,000,000 DAI

For this pool, $D =\,$ 3,000,000.

If a swap of 100,000 USDC were to be made for DAI, 100,000 DAI would be received and the new pool balances would be:

- 1,100,000 USDC
- 1,000,000 USDT
- 900,000 DAI

At first this seems like the perfect invariant for a pool of stablecoins. It implies that all stablecoins are worth exactly the same amount. To understand the shortfalls of this invariant we need to look at what happens if one of the coins loses its peg.

Consider the scenario where USDT loses its peg. If USDT were to now become worth \\\$0.50, there would be an arbitrage opportunity in the pool. Someone could buy USDT at \\\$0.50 elsewhere, and swap it in the pool for USDC or DAI. The pool would very quickly be drained of USDC and DAI in this scenario. While this may seem like a non-issue since no one would want to hold USDT if it dropped to \\\$0.50 anyways, it should be recognized that this same arbitrage opportunity is present if USDT could be bought elsewhere at any price not exactly equal to \\\$1. The result would be a pool where the underlying token supplies are highly volatile depending on arbitrage opportunities.


## Constant Product Invariant
The constant product invariant is likely the most familiar invariant. This is the invariant used by Uniswap V2 type of pools with $n=2$.

$$
\prod x_i = \left ( \frac{D}{n} \right )^n
$$

I will not go into much detail on the constant product invariant here as it has been covered extensively by others. One thing that may look different in this formulation is the right hand side of the equation. For a Uniswap V2 pool, the invariant is typically stated as $xy=k$, in this case we can recover $D$ through a simple change of variables: $k = (D/2)^2$.

## StableSwap Invariant
We will now begin exploring the StableSwap invariant and its derivation from the constant sum and constant product invariants.

$$
A n^n \sum x_i + D = A D n^n + \frac{D^{n+1}}{n^n \prod x_i}
$$

The idea behind the StableSwap invariant is to combine the stability of the constant sum invariant with the constant product invariant. The two invariants could be combined by averaging between them in a formulation such as

$$
\lambda \sum x_i + (1 - \lambda) \prod x_i = \lambda D + (1 - \lambda) \left ( \frac{D}{n} \right )^n
$$

where $0 \le \lambda \le 1$. When $\lambda = 0$ we have the constant product invariant, and when $\lambda = 1$ we have the constant sum invariant.

Another way they could be combined is as

$$
\lambda \sum x_i + \prod x_i = \lambda D + \left ( \frac{D}{n} \right )^n
$$

where $0 \le \lambda < \infty$. When $\lambda = 0$ we get the constant product invariant and as $\lambda \to \infty$, we converge towards the constant sum invariant.

Both of the above formulations are equivalent to the StableSwap invariant under a change of variables.

The setup used in the StableSwap invariant combines the constant product and constant sum in a way more similar to the second version above. They multiple the constant sum invariant by $\chi D^{n-1}$ and add it to the constant product invariant (substitute $\lambda = \chi D^{n-1}$ above)

$$
\chi D^{n-1} \sum x_i + \prod x_i = \chi D^n + \left ( \frac{D}{n} \right )^n
$$

Next, they substitute

$$
\chi = \frac{A \prod x_i}{(D/n)^n}
$$

introducing the **amplification coefficient** $A$. Finally, after rearranging some terms, we get the StableSwap invariant

$$
A n^n \sum x_i + D = A D n^n + \frac{D^{n+1}}{n^n \prod x_i}
$$

An important property of the StableSwap invariant is that $D$ remains constant when there are swaps in the pool, the same property as in the constant sum and constant product invariants.

---

In the upcoming posts in the series, we will dive deeper into some of the dynamics behind the StableSwap invariant.
