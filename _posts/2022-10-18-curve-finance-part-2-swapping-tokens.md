---
title: "Curve Finance Part 2: Swapping Tokens"
categories:
  - Blog
excerpt: "This post explores the dynamics of the StableSwap invariant when swapping tokens."
---

In the previous post, the Curve StableSwap invariant was introduced

$$
A n^n \sum x_i + D = A D n^n + \frac{D^{n+1}}{n^n \prod x_i}
$$

This post will explore the dynamics of the StableSwap invariant when swapping tokens.

{% capture notice-2 %}
__Example 1:__ Consider a liquidity pool ([3pool](https://curve.fi/3pool)) composed of

- 1,000,000 DAI
- 1,200,000 USDC
- 750,000 USDT

with $A = 100$. How much DAI would be received when swapping 120,000 USDC to DAI?
{% endcapture %}

<div class="notice--success">
  {{ notice-2 | markdownify }}
</div>

## Swapping under the StableSwap Invariant
Under the StableSwap invariant, swaps in the pool do not affect the value of $D$. Thus, to determine how much of token $k$ is received when swapping with token $j$, we must solve the StableSwap invariant for $x_k$ given the new value for $x_j$.

Let the $j$th token be the token we are swapping out of, and let the $k$th token be the one we wish to receive.

Let $x_j' = x_j + \alpha$ where $\alpha$ is the amount of token $j$ we will be swapping (a known value). Let $x_k' = x_k - \beta$ where $\beta$ is the amount of token $k$ that we will receive. Let $x_i' = x_i \,\forall\, i \ne j,k$.

To determine the value of $\beta$, we must solve the StableSwap invariant for $x_k'$:

$$
A n^n \sum x_i' + D = A D n^n + \frac{D^{n+1}}{n^n \prod x_i'}
$$

This can be solved either analytically or using an iterative method.

### Analytical Solution
To solve the StableSwap invariant for $x_k'$ analytically, we can rearrange the StableSwap invariant as a quadratic polynomial in $x_k'$ of the form $a x_k'^2 + b x_k' + c$ with

$$
\begin{align*}
a &= A n^{2n} \prod_{i \ne k}{x_i'}\\
b &= \left (D - A D n^n + A n^n \sum_{i \ne k}{x_i'} \right ) n^n \prod_{i \ne k}{x_i'}\\
c &= -D^{n+1}
\end{align*}
$$

Thus, we have

$$
x_k - \beta = x_k' = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$

taking the positive solution for $x_k'$. I will not go into further analysis on whether you are guaranteed a real positive solution but it could likely be done.

To answer Example 1, solving the StableSwap invariant we find we would receive 119,962 DAI.

## Analysis

### Slippage
Let's take a look at the amount of slippage in a Curve pool. Consider a pool that contains 2 tokens in equal proportions

- 500,000 USDC
- 500,000 USDT

For $A = 100$, how much slippage would there be for swapping 1,000 USDC for USDT? How about for 10,000, or 100,000?

<figure class="align-center" style="width: 700px;">
	<img src="{{ site.asset_url }}/curve-finance-part-2-swapping-tokens/Swap_Slippage_Percentage.png"    
		 srcset="{{ site.asset_url }}/curve-finance-part-2-swapping-tokens/Swap_Slippage_Percentage.png 1x, {{ site.asset_url }}/curve-finance-part-2-swapping-tokens/Swap_Slippage_Percentage@2x.png 2x" 
		 alt="" 
		 width="800" 
		 height="540">
	<figcaption><b>Figure 1:</b> Percent slippage for swapping USDC to USDT in a pool initially consisting of 500,000 USDC and 500,000 USDT under the constant product and StableSwap models.
	</figcaption>
</figure>

We can see that the slippage on the trade remains fairly small until the swap size goes above about 500,000 USDC. The upper limit on the amount of USDT we can receive by swapping is of course the amount of USDT in the pool, 500,000 USDT. Swapping 500,000 USDC for USDT in this pool would result in 476,219 USDT. For comparison, under a constant product model (e.g. Uniswap V2), you would receive 250,000 USDT. The slippage on swapping in the Curve pool remains significantly lower than a constant product pool.

### Impact of Amplification Coefficient on Slippage
The amplification coefficient $A$ has a significant impact on the amount of slippage that will occur for swaps. Larger values of $A$ lead to lower slippage.

<figure class="align-center" style="width: 700px;">
	<img src="{{ site.asset_url }}/curve-finance-part-2-swapping-tokens/Percent_Slippage_by_A.png"    
		 srcset="{{ site.asset_url }}/curve-finance-part-2-swapping-tokens/Percent_Slippage_by_A.png, {{ site.asset_url }}/curve-finance-part-2-swapping-tokens/Percent_Slippage_by_A@2x.png 2x" 
		 alt="" 
		 width="600" 
		 height="400">
	<figcaption><b>Figure 2:</b> Percent slippage for swapping USDC to USDT in a pool initially consisting of 500,000 USDC and 500,000 USDT for a range of amplification coefficients.
	</figcaption>
</figure>

### Swapping in Imbalanced Pools
So far we have only looked at balanced pools. That is, pools with equal proportions of tokens. When pools become imbalanced, the amount of slippage on a swap can be large even for fairly small swaps.

Consider the same pool consisting of USDC and USDT as before except this time the proportions of the tokens are not 50/50. We will fix the total supply of tokens in the pool to 1,000,000 ($\sum x_i =\,$1,000,000) and calculate how much USDT we would receive by swapping 10,000 USDC if the proportion of the pool that is USDC ranges from ~1% to 99%. Figure 3 shows the amount of USDT we would receive. For the proportion between about 20% to 80% with $A=100$, the swap remains fairly stable. Below 20%, we receive significantly more USDT than USDC. Similarly, when the proportion is above 80%, we receive significantly less USDT than USDC supplied. We can again see that the value of $A$ has a significant impact on the slippage with lower values leading to much larger slippage.

<figure class="align-center" style="width: 700px;">
	<img src="{{ site.asset_url }}/curve-finance-part-2-swapping-tokens/Imbalanced_Pool_Slippage.png"    
		 srcset="{{ site.asset_url }}/curve-finance-part-2-swapping-tokens/Imbalanced_Pool_Slippage.png, {{ site.asset_url }}/curve-finance-part-2-swapping-tokens/Imbalanced_Pool_Slippage@2x.png 2x" 
		 alt="" 
		 width="600" 
		 height="400">
	<figcaption><b>Figure 3:</b> Slippage in swapping 10,000 USDC to USDT for a range of USDC proportion in the pool with fixed total token supply of 1,000,000.
	</figcaption>
</figure>

