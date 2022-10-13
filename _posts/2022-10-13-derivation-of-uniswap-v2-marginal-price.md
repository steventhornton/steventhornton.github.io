---
title: "Derivation of Uniswap V2 Marginal Price"
categories:
  - Blog
excerpt: "The marginal price for a Uniswap V2 (constant product) style AMM is typically quoted without working through the mathematical deriviation. In this post I work through the derivation of the marginal price formula."
---

The marginal price for a Uniswap V2 style pool is quoted as

$$
p = \frac{y}{x}
$$

The typical explanation for why this formula holds usually follows the form:

> Under the Uniswap V2 (constant product) model, the pool always contains equal dollar amounts of the two tokens. For example, if a pool has \\\$1,000,000 of token $X$, it must also contain \\\$1,000,000 of token $Y$. Therefore, the exchange rate between token $X$ and $Y$ is given by $y/x$ where $x$ and $y$ represent the amounts of tokens $X$ and $Y$ in the pool respectively.

While this is correct, and not a bad explanation, I was interested in how this works out mathematically.


## Swapping under the Uniswap V2 Model
Before I derive the above price formula, we must first look at how swaps work in Uniswap V2 (constant product, $n=2$) style pools. The dynamics of these pools are governed by the invariant

$$
xy=k
$$

where $x$ is the amount of token $X$, $y$ is the amount of token $Y$, and $k$ is held constant under swaps.

Based on this model, if we were to swap $\alpha$ of token $X$ for token $Y$, we can determine the amount $\beta$ of token $Y$ we receive by solving the invariant for $\beta$ after adjusting for the new balances. The adjusted balances after the swap will be $x+\alpha$ for token $X$, and $y-\beta$ for token $Y$, thus we have:

$$
(x + \alpha) (y - \beta) = k
$$


Solving for $\beta$ we find the amount of token $Y$ we receive is

$$
\beta = \frac{xy + \alpha y - k}{x+\alpha}
$$


which can be simplified to

$$
\beta = \frac{\alpha y}{x + \alpha}
$$

Clearly $\beta$ is a function of $\alpha$ which makes sense as the larger the swap, the more slippage there will be on the trade. We can quote the trade price of token $X$ in units of token $Y$ as

$$
p(\alpha) = \frac{\beta}{\alpha}
$$

## Swap Example
Let’s run through a quick example of what this looks like. Consider the ETH/USDC pool ($X=\,$ETH, $Y=\,$USDC) where:

- $x = 500$
- $y = 1,000,000$

Based on the price formula presented initially, this implies the marginal price of ETH in USDC is $1,000,000 / 500 = 2,000$.

If we were to swap $\alpha = 10$ ETH for USDC in this pool, we would receive $\beta = 19,607$ USDC giving a trade price of $\beta / \alpha = 1960$, less than the price of 2,000 as implied by the marginal price formula. For larger swaps, we deviate even farther from the marginal price. Swapping $\alpha = 100$ ETH for USDC would give a trade price of 1,666.

## Derivation of Marginal Price Formula
The marginal price can be thought of as the trade price for swapping a very very small amount of token $X$ for $Y$. An amount so small that there is no slippage on the trade. Mathematically, this is given by the limit

$$
\text{Marginal Price} \equiv \lim_{\alpha \to 0} \frac{\beta}{\alpha}
$$


that is, the trade price converges to the marginal price as the trade size ($\alpha$) goes to 0.

Substituting in the value for $\beta$ used for determining swap size, we find

$$
\begin{align*}
    \text{Marginal Price} &= \lim_{\alpha \to 0} \frac{\beta}{\alpha}\\
&= \lim_{\alpha \to 0} \frac{\alpha y}{\alpha(x+\alpha)}\\
&= \lim_{\alpha \to 0} \frac{y}{x+\alpha}\\
&= \frac{y}{x}
\end{align*}
$$

This same approach can be used to determine the marginal price under other AMM models. In future posts I will work through the same calculation for the models used by DEXs such as [Curve](https://curve.fi/), [Balancer](https://balancer.fi/), and [Solidly](https://solidly.exchange/).



