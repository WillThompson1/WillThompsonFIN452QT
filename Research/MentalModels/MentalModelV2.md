# **Mental Model Framework: Cluster-Based Mean Reversion**

## **1\. Framing the Problem (Design Thinking)**

The project is framed through the lens of **Design Thinking**, moving from empathy for the market's complexity to a tested prototype.

* **The Core Problem**: S\&P 500 sectors often diverge due to temporary shocks (noise), yet they share a structural bond as part of the same index. The goal is to determine when a sector has moved "too far" from its peers in a way that signalizes a mean-reversion opportunity rather than a permanent structural break.  
* **Uncertainty & Variables**: We face uncertainty in whether price movements are "noise" or "trend". We treat the 11 Sector ETFs and the SPY benchmark as the primary interrelated variables.  
* **Boundary Conditions (Constraints)**:  
  * **Data Frequency**: Daily (High noise level).  
  * **Execution**: Monthly rebalancing (Requires signals that persist for more than a few days).  
  * **Architecture**: R and Tidymodels only (Enforces a mechanical, disciplined implementation).

## **2\. The Wisdom Hierarchy: From Data to Wisdom**

We apply the course's **Wisdom Hierarchy** to structure our workflow:

* **Data**: Raw daily OHLC price data for 11 sector ETFs and SPY.  
* **Information**: Returns and correlation matrices derived from clustering.  
* **Knowledge**: Identifying causative relationships where an ETF's deviation from its cluster "fair value" (via Kalman Filter) suggests a future return.  
* **Wisdom**: Knowing when to execute a trade based on the magnitude of the signal and the risk-reward ratio, despite market uncertainty.

## **3\. The "Many Models" Approach (The Toolbox)**

Consistent with Scott Page’s **Many Models** approach, we use an ensemble of frameworks to view the problem:

1. **Systems Thinking (Clustering)**: We do not view ETFs in isolation. We use **Clustering** to define "neighborhoods" of sectors (e.g., grouping XLE and XLB during commodity cycles). This ensures the model only compares "apples to apples."  
2. **Signal vs. Noise (Kalman Filter)**: Daily data is inherently noisy. We use the **Kalman Filter** as a trend-filtering lens to estimate the latent "fair value" of an ETF relative to its cluster.  
3. **The Scientific Method (Tidymodels)**: The strategy is not a "statistical fluke". We use a disciplined **Tidymodels** workflow to separate training (2000-2024) from testing (2025-2026), treating our backtest as a rigorous experiment.

## **4\. Strategic Logic & Implementation (QT Framework)**

The model follows a purely **mechanical process**:

* **Signal Generation**: A signal is issued when the residual (distance between the current price and the Kalman-estimated fair value) exceeds a specific threshold relative to its cluster peers.  
* **Execution Strategy**: We assume execution occurs at the monthly rebalancing date. We must account for **slippage** and the fact that "Open" prices are rarely hit exactly in real-world trading.  
* **Risk Reward**: Monthly rebalancing acts as a natural "cooling-off" period, preventing the over-trading of daily noise while managing the risk of holding a diverging position for too long.

## **5\. Inversion: Addressing Drawbacks & Vulnerabilities**

Using the **Inversion** mental model, we identify how the model fails:

* **The "Structural Break" Risk**: Mean reversion assumes the relationship returns to normal. If a sector undergoes a fundamental change (e.g., the 2008 Financial Crisis for XLF), the model will keep buying a "falling knife."  
* **Look-ahead Bias**: We must ensure our clusters are formed only using information available at the time of the rebalance.  
* **Overfitting (The Data Scientist's Curse)**: With daily data over 24 years, there is a risk of finding "ghost patterns." We mitigate this by keeping the model simple and grounded in economic rationale rather than complex statistical flukes.

## **6\. Final Synthesis**

**This strategy treats the S\&P 500 sectors as an interconnected system, uses clustering to define logical peer groups, applies a Kalman Filter to extract a signal from noisy daily data, and enforces a monthly rebalancing discipline to arbitrage temporary price dislocations back toward fair value.**

