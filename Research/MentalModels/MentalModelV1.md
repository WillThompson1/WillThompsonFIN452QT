## **1\. Mental model framework** 

### **Mental Model Framework for the Project**

#### **Project framing**

The real-world problem is not simply to find which sector ETF has gone up or down the most. The problem is to decide whether temporary divergences among S\&P 500 sector ETFs contain a tradable signal once sectors are first grouped into behaviorally similar sets. This project therefore treats the market as a system of related sector sleeves rather than a list of isolated ETFs. The objective is to identify cases where a sector has moved too far away from the behavior of its most comparable peers and may partially revert toward a latent fair relationship. This framing is consistent with the course mental models material, which emphasizes beginning with a real-world problem, identifying uncertainty, defining relationships, and sketching the framework before coding.

#### **Why this strategy fits the assignment**

This strategy was chosen because it fits the professor’s constraints in a direct and defensible way. Clustering is not an afterthought. It is the primary step that defines which ETFs should be compared with one another. The Kalman filter is also not decorative. It is used to estimate a smoothed latent equilibrium or fair-value relationship for each ETF relative to its cluster. That means the two required concepts are both central to the strategy architecture rather than being added mechanically at the end. The strategy also fits the allowed strategy family in the class QT notes, where mean reversion includes pairs trading, statistical arbitrage, and range-based approaches.

#### **Core market view**

The market view behind the strategy is as follows:

Sector ETFs are linked through shared exposure to the same broad equity market, but they do not always react to shocks, flows, macro news, and sentiment at the same speed. At times, groups of sectors behave similarly enough that they can be treated as a cluster. Within those clusters, one ETF may temporarily overshoot or undershoot its peers because of noise, overreaction, short-term positioning, or regime-specific stress. Those deviations are not assumed to disappear immediately, but they may revert partially over time. The purpose of the model is to identify these deviations in a systematic way and only trade them when the deviation appears meaningful relative to a smoothed estimate of fair value.

#### **The mental models being used**

This project uses an ensemble of mental models rather than a single lens, which matches the course notes’ emphasis on using a toolbox of models to make sense of complexity.

**1\. Systems thinking**  
 The sector ETF universe is treated as an interconnected system. The signal on one ETF is not interpreted in isolation. It is interpreted relative to the behavior of similar sectors, the benchmark SPY, and the state of the broader cluster structure. This is the main conceptual reason for using clustering first.

**2\. Inversion**  
 Instead of asking, “Which sector should I buy?”, the model first asks, “What would cause a false signal?” A false signal would likely come from comparing ETFs that are not truly similar, using raw noisy deviations, overfitting thresholds to the training sample, or treating temporary structural breaks as mispricing. This inversion lens forces the design to include clustering, smoothing, risk filters, and walk-forward separation between training and testing.

**3\. Margin of safety**  
 The model should not trade every small deviation. A trade should only occur when the residual gap between an ETF and its cluster fair-value estimate is large enough to justify action. This creates a buffer against noise and implementation error. The margin of safety in this project is operationalized through signal thresholds, confirmation logic, and limited position sizing.

**4\. Risk-reward framework**  
 The project is not only about generating signals. It is also about deciding whether a deviation is worth trading relative to the risks of false mean reversion, prolonged dislocation, turnover, and drawdown. This fits both the mental models notes and the QT material on defining risk-reward criteria and assessing performance beyond raw return alone.

**5\. Second-order thinking**  
 A sector that has moved away from its cluster may not immediately revert. The second-order question is whether the deviation reflects a genuine structural change rather than a temporary dislocation. This is why the model must distinguish between short-term overextension and a change in the underlying relationship. The Kalman filter helps because it estimates a latent smooth process rather than reacting directly to raw noisy observations.

**6\. Opportunity cost**  
 Every portfolio slot allocated to one trade means not allocating it elsewhere. The strategy therefore needs a disciplined selection process rather than opening many weak trades. Opportunity cost supports ranking signals and only selecting the strongest dislocations each rebalance date.

#### **Problem definition in model terms**

The strategy answers the following question:

Among the sector ETFs, which ones are currently misaligned relative to the behavior of their most comparable peers, and are those deviations large and reliable enough to justify a monthly long or short position?

This converts the broad project prompt into a more precise modeling problem:

* define similarity,  
* group ETFs into clusters,  
* estimate each ETF’s deviation from its cluster,  
* smooth the deviation using Kalman filtering,  
* only trade when the deviation is large enough and directionally defensible,  
* then rebalance monthly.

#### **Inputs, relationships, and outputs**

The mental models notes say a good framework should define inputs, their directional relationship to outputs, and the nature of those relationships. That is exactly how this project will be structured.

**Inputs**

* Daily adjusted prices for SPY and the sector ETFs  
* Daily returns  
* Rolling volatility measures  
* Rolling correlations and beta-like co-movement measures  
* Relative performance measures versus SPY  
* Cluster membership labels  
* Cluster residual or spread measures  
* Kalman-smoothed fair-value estimate and deviation from that estimate

**Directional relationships**

* Higher similarity in rolling behavior increases the likelihood that two ETFs belong in the same cluster.  
* Larger positive deviation above cluster fair value increases the case for a short signal.  
* Larger negative deviation below cluster fair value increases the case for a long signal.  
* Greater noise or instability reduces confidence in the signal.  
* Larger absolute deviation only matters if it is measured relative to a stable cluster relationship.

**Outputs**

* Monthly cluster assignment  
* Monthly residual score for each ETF  
* Long, short, or neutral decision  
* Portfolio weights  
* Daily P\&L path between rebalances  
* Performance and robustness metrics

#### **Strategy architecture**

The mental model for this project has five layers.

**Layer 1: Market structure layer**  
 The sector ETFs are not treated as interchangeable, but neither are they treated as fully separate. They are part of one market system and sometimes form natural behavioral groups.

**Layer 2: Clustering layer**  
 At each rebalance date, the ETFs are clustered using daily-derived features that capture recent behavior. The purpose of clustering is to create comparable sets. This is the primary signal driver because the strategy does not define fair value globally. It defines fair value relative to the ETF’s current cluster.

**Layer 3: Deviation layer**  
 Within each cluster, compute each ETF’s deviation from a cluster benchmark, such as cluster-average relative return, cluster centroid behavior, or a cluster basket series. This creates a candidate mean-reversion signal.

**Layer 4: Kalman layer**  
 Apply the Kalman filter to the deviation process to estimate a smoothed latent fair-value relationship and avoid responding to raw noise. Kalman therefore converts a noisy residual into a more defensible state estimate.

**Layer 5: Decision layer**  
 Only trade when the observed deviation is sufficiently large relative to the Kalman-smoothed estimate and passes the chosen threshold logic. Then rank eligible trades and form a monthly portfolio.

#### **Implementation philosophy**

The implementation should follow the design-thinking style in your notes: empathize with the project constraints, define the modeling problem clearly, ideate candidate approaches, prototype a minimum viable implementation, and then test and refine it. In practical terms, that means the model is built in stages rather than all at once.

**Stage 1: Define the universe and constraints**  
 Use SPY plus the required sector ETFs only. Use daily data only. Use API-loaded data only. Keep the training period separate from the testing period. Rebalance monthly.

**Stage 2: Define clustering features**  
 The first design choice is how similarity is measured. A sensible starting set is daily-derived rolling features such as 20-day and 60-day volatility, 60-day correlation to SPY, recent relative returns versus SPY, and maybe rolling drawdown or beta. The exact feature set should stay compact enough to remain interpretable.

**Stage 3: Define cluster-relative deviation**  
 Once clusters are formed, define a cluster benchmark and compute each ETF’s deviation from it. This is the raw mean-reversion signal.

**Stage 4: Define the Kalman state**  
 Use the Kalman filter on the deviation series to estimate the latent equilibrium or smoothed fair-value gap. The trading signal comes from the difference between the observed residual and the filtered residual.

**Stage 5: Define decision rules**  
 Translate the signal into long, short, or neutral positions at month-end.

**Stage 6: Validate and iterate**  
 Use the training period to choose a reasonable set of parameters and a robust region rather than a single fragile optimum. Then backtest only on the held-out testing period. The QT notes explicitly frame parameter selection, risk-reward criteria, and separate backtesting as part of the proper workflow.

#### **Decision rules**

These should appear clearly in the document because the brief explicitly requires clear decision rules.

A clean first version of the rules is:

1. **At each month-end**, use daily data up to that date only.  
2. **Cluster all sector ETFs** using the selected rolling features.  
3. **For each ETF**, compute its cluster-relative residual.  
4. **Run the Kalman filter** on that residual series.  
5. **Generate a long signal** if the ETF is materially below its Kalman-smoothed cluster fair value.  
6. **Generate a short signal** if the ETF is materially above its Kalman-smoothed cluster fair value.  
7. **Generate no trade** if the deviation is too small, the cluster is unstable, or the signal is ambiguous.  
8. **Rank signals by absolute conviction** and only hold the strongest positions.  
9. **Rebalance monthly** and hold until the next rebalance unless the model design later introduces an exit override.  
10. **Evaluate** on cumulative return, drawdown, Sharpe or Omega, turnover, hit rate, and signal stability rather than return alone. The course discussion notes specifically mention Omega and drawdowns, and the QT notes discuss both risk-reward criteria and the danger of models driven by outliers or excessive turnover.

#### **Assumptions**

This model depends on several assumptions:

* Similar sector ETFs can be meaningfully grouped using recent observable behavior.  
* Temporary within-cluster divergences sometimes reflect tradable misalignment rather than permanent structural change.  
* The Kalman filter provides a better estimate of latent fair value than the raw residual alone.  
* Monthly rebalancing is sufficient to capture medium-horizon mean reversion within this ETF universe.  
* The best goal of the project is not maximizing historical return at any cost, but implementing a coherent, defensible, and testable model consistent with the assignment.

#### **Boundary conditions and limitations**

The mental models notes explicitly ask whether boundary conditions matter. They do here.

* Sector relationships can break during major macro regime changes.  
* XLC and XLRE have shorter histories than older sector ETFs, which may affect feature design or require careful treatment of available windows.  
* A cluster built from only a small ETF universe can be unstable if too many features are used.  
* Mean reversion can fail if the divergence reflects a real structural repricing rather than noise.  
* Backtest success alone does not prove the model is economically correct. The QT notes explicitly warn against relying on backtesting as if it were a scientific proof.

#### **How this framework reduces bias**

This framework is designed to reduce common modeling mistakes:

* **Look-ahead bias** is reduced by using only information available at each rebalance date.  
* **Data mining** is reduced by keeping the model architecture grounded in a prior market view rather than trying many unrelated ideas.  
* **Storytelling bias** is reduced by stating the market hypothesis and decision rules before coding.  
* **Overfitting** is reduced by preferring stable parameter regions and by separating training from testing.  
* **Implementation bias** is reduced by making the code mirror the diagram and the written logic.

#### **End-state of the mental model**

The final mental model can be summarized in one sentence:

**This project treats sector ETFs as an interconnected system, uses clustering to identify comparable groups, uses cluster-relative residuals to detect temporary dislocations, uses the Kalman filter to estimate smoothed fair value, and only trades monthly when the deviation is large enough to justify a mean-reversion position.**

That sentence is short enough for the presentation, but the framework above explains why each step exists and how the code must later follow from it.

