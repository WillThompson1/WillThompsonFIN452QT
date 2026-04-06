
# Quantitative Trading Project Context File
Last updated: 2026-04-05

## Purpose of this file

This file is intended to bring a new AI model or new chat fully up to speed on the quantitative trading final project without needing to re-read the full conversation. It captures the project constraints, the strategy direction selected, the reasoning behind that choice, the mental model framework, the expected implementation logic, the key deliverables, and the unresolved design decisions that still need to be finalized before coding.

This file should be treated as the governing context for future work on this project. The later R/Quarto code is expected to follow the logic described here. The mental model is not optional decoration. It is the conceptual framework that will drive the code implementation, the decision rules, the presentation narrative, and the final written explanation.

---

## 1. Project overview

This is an individual final project for a Financial Analytics and Trading course. The project requires developing and fully implementing a quantitative trading model in R, following the QT trading chapter covered in class. The final submission must include:

- a `.qmd` Quarto document
- a self-contained HTML output
- a final `context.md` file
- a public GitHub repository with code and data access logic
- daily commit history showing work progression
- a professional presentation with clear logic and visualization
- a mental model diagram created using Mermaid.ai

The presentation is approximately 10 minutes and the project is worth 20 percent of the course grade.

The project is heavily implementation-focused. The quality of reasoning, clarity of decision rules, consistency between mental model and code, and professional structure appear to matter more than maximizing historical return at any cost.

---

## 2. Non-negotiable assignment constraints

These are fixed by the project instructions and professor clarification.

### Allowed universe
Use SPY and the S&P 500 Select Sector SPDR ETFs as the strategy universe:

- SPY, benchmark
- XLB, Materials
- XLC, Communication Services, available from 2018
- XLE, Energy
- XLF, Financials
- XLI, Industrials
- XLK, Technology
- XLP, Consumer Staples
- XLRE, Real Estate, available from 2015
- XLV, Health Care
- XLU, Utilities
- XLY, Consumer Discretionary

### Time periods
- Training period: June 2000 to December 2024
- Testing period: January 2025 to March 2026 inclusive

### Data and workflow constraints
- Data frequency must be daily
- Trading framework must use daily data
- Portfolio rebalancing occurs monthly
- All price and indicator data must be loaded through APIs
- No local CSV or Excel source files are allowed for the model inputs
- All analysis must be done in R inside the Quarto document
- Python should not be used in the submitted analysis or code structure
- Code should follow a tidymodels workflow

### Strategy architecture constraints
- Clustering must be used as a primary method to drive signals from the indicator mental model
- Kalman Filter must be used in conjunction with clustering in triggering a trade signal
- The document must make the decision rules very clear
- The mental model must be visualized in the document using Mermaid.ai
- The code must match the mental model

### Important professor clarifications from class Q and A
- The project should be approached like an active manager trying to add value beyond just holding the index
- Monthly rebalancing is the expected interpretation
- Shorting ETFs is acceptable in principle, assuming the instrument can be shorted and margin-call mechanics are ignored for project purposes
- Clustering features and indicators are left to the student to choose, so long as they fit the investment mandate and assignment requirements

---

## 3. High-level project interpretation

The professor has already specified the strategy shell, but not the exact strategy implementation.

The fixed shell is:
- sector ETF trading
- daily data
- monthly rebalancing
- clustering is primary
- Kalman is required
- benchmark-aware active management
- fully codified decision rules

The student still has to choose:
- the actual market hypothesis
- the exact signal definition
- the features used for clustering
- how Kalman is applied
- the long, short, and neutral decision rules
- the portfolio construction logic
- the parameter tuning and robustness process
- the evaluation metrics

---

## 4. Strategy directions considered

Several candidate directions were considered before making the final selection.

### Considered options
- Relative-strength sector momentum
- Hierarchical momentum
- Risk-managed or crash-aware momentum
- Pure time-series momentum
- Factor-based sector rotation
- Regime-switching sector rotation
- Sentiment-enhanced sector rotation
- Cluster-relative mean reversion

### Final chosen direction
The chosen approach is:

**Cluster-relative mean reversion on the S&P 500 sector ETFs, with clustering as the primary signal engine and Kalman filtering used to smooth and validate cluster-relative dislocations.**

### Why this direction was chosen
This strategy was chosen primarily because it fits the professor’s required concepts very naturally.

- Clustering is central rather than superficial. It determines which ETFs are comparable at each rebalance date.
- Kalman is central rather than decorative. It is used to smooth the cluster-relative deviation process and estimate a latent fair-value relationship.
- Mean reversion belongs to an accepted strategy family from the course notes, including pairs trading, statistical arbitrage, and range-based logic.
- The project is being graded on implementation quality and defensibility, not solely on maximizing historical returns.
- Compared with momentum, this approach makes it easier to justify both clustering and Kalman as essential parts of the strategy rather than add-ons.

### Important project philosophy
The goal is not to over-optimize for whichever strategy happened to work best in one interval. The goal is to design a coherent and defensible model whose logic is clear, whose rules are codified, and whose implementation aligns tightly with the assignment requirements.

---

## 5. Core market hypothesis

The market hypothesis behind the chosen strategy is:

Sector ETFs are part of one broad equity market system, but they do not always move in lockstep. At times, subsets of sectors behave similarly enough that they can be treated as a comparable cluster. Within a cluster, one ETF may temporarily overshoot or undershoot the rest because of noise, overreaction, short-term positioning, or transitory regime pressure. If the cluster remains behaviorally coherent, those deviations can contain a tradable mean-reversion signal.

The model therefore does not ask:
- which sector had the highest recent return?
- which sector is strongest in absolute terms?

Instead, it asks:
- which sectors currently belong together?
- what is the fair relationship within that comparable group?
- which ETF has moved materially away from that cluster-relative fair value?
- is that deviation large enough, stable enough, and smooth enough to justify a monthly trade?

This is a relative-value view, not a simple directional trend-following view.

---

## 6. Full mental model framework

The mental models section is central to this project. The mental model is intended to drive the code, not merely describe it after the fact.

### 6.1 Mental models used

This project uses an ensemble of mental models rather than one single lens.

#### Systems thinking
The sector ETF universe is treated as an interconnected system rather than a list of isolated assets. Signals are interpreted relative to peer behavior, cluster structure, and the benchmark, not in isolation.

#### Inversion
The project is framed partly by asking what would cause failure:
- comparing assets that are not truly similar
- trading noise instead of meaningful dislocation
- overfitting thresholds to the training sample
- mistaking structural change for temporary mispricing
- relying on storytelling after the fact

This inversion step is important because it directly motivates clustering, Kalman smoothing, walk-forward separation, and robust parameter selection.

#### Margin of safety
The model should not trade every small deviation. A trade should require a sufficiently large and defensible gap between the observed cluster-relative residual and the Kalman-smoothed fair-value estimate. This protects against noise and false positives.

#### Risk-reward framework
Signals are not enough by themselves. The model must weigh expected opportunity against volatility, drawdowns, trade concentration, turnover, and instability. This affects threshold selection, portfolio size, and final evaluation metrics.

#### Second-order thinking
A deviation may not revert simply because it exists. It may represent a real change in the economic or market relationship. The model therefore needs to distinguish temporary dislocation from structural repricing. Kalman is part of that solution because it smooths state estimates instead of reacting to every noisy move.

#### Opportunity cost
Every trade takes up capital and attention. The portfolio should favor the strongest relative-value opportunities rather than open weak positions everywhere. This supports ranking and signal filtering.

### 6.2 Mental model summary statement

The governing mental model is:

**Treat sector ETFs as an interconnected system, use clustering to identify comparable groups, measure how far each ETF has diverged from its cluster-relative fair relationship, use the Kalman filter to estimate a smoothed latent equilibrium, and trade only the largest and most defensible monthly mean-reversion opportunities.**

### 6.3 Why the mental model must drive the code

The project requirements explicitly state that the code must match the mental model. Therefore, the later implementation should follow this exact chain:

1. define the market problem
2. define the observable inputs
3. define comparability through clustering
4. define dislocation through a cluster-relative residual
5. define signal quality through Kalman smoothing
6. define explicit long, short, and neutral decision rules
7. define monthly portfolio construction
8. define evaluation and iteration

If future coding decisions drift away from this sequence, the project risks becoming inconsistent with the assignment and with the narrative in the final presentation.

---

## 7. Problem framing in mental-model terms

### Real-world problem
Can temporary divergences among S&P 500 sector ETFs be traded if sectors are first grouped into behaviorally similar clusters?

### Uncertainty
Yes. Uncertainty is central:
- sector relationships vary over time
- the same deviation may mean noise in one regime and structural change in another
- cluster structure may shift
- mean reversion may occur slowly or not at all
- signal strength is not directly observable

### Nature of the problem
This is not a pure directional forecast problem. It is a relative-value problem inside a dynamic system of related assets.

### Boundary conditions
Boundary conditions matter:
- the universe is small
- some ETFs have shorter histories, especially XLC and XLRE
- the project requires monthly rebalancing even though daily data is used
- the benchmark SPY matters for interpretation
- model complexity must remain explainable inside a 10-minute presentation

### End-game
The final output must be:
- a logically structured Quarto report
- R code that mirrors the model
- a Mermaid diagram that shows the same flow
- a presentation that clearly explains the decision rules
- a defensible strategy, even if historical return is not spectacular

---

## 8. Strategy architecture

The strategy has five conceptual layers.

### Layer 1: Market structure
All sector ETFs are part of the same broad market system, but subsets of them may behave similarly over rolling windows.

### Layer 2: Clustering
At each monthly rebalance date, use daily-derived rolling features to group ETFs into behaviorally similar clusters.

This is the primary signal engine.

Clustering determines:
- who is comparable to whom
- which ETFs belong in the same relative-value comparison set
- how fair value is defined

Fair value is **cluster-relative**, not global.

### Layer 3: Residual construction
Within each cluster, define a benchmark or reference process and measure each ETF’s deviation from it.

Possible cluster benchmarks:
- cluster average relative return
- cluster centroid behavior
- cluster basket series
- average of standardized features converted into a reference score

The output is an observed cluster-relative residual or dislocation measure.

### Layer 4: Kalman filtering
Apply the Kalman filter to the cluster-relative residual series.

Purpose:
- estimate a smoothed latent equilibrium
- reduce noise
- avoid reacting to raw residual spikes
- improve signal reliability

Kalman does not replace clustering. It refines the cluster-relative signal.

### Layer 5: Decision and portfolio construction
Use the gap between the observed residual and the Kalman-smoothed state to decide whether to go long, short, or remain neutral.

Then:
- rank signals by strength
- select only the best opportunities
- rebalance the portfolio monthly

---

## 9. Planned implementation logic

This section describes how the later code is expected to work at a high level.

### Step 1: Data acquisition
Pull daily data via APIs in R for:
- SPY
- the required sector ETFs

Potential data packages or access routes may include:
- `tidyquant::tq_get()`
- `quantmod::getSymbols()`
- any acceptable API path that remains fully inside R and Quarto

### Step 2: Preprocessing
- adjust price series appropriately
- compute daily returns
- align all series on a consistent daily calendar
- restrict all modeling choices to information available at each point in time
- isolate the testing period so it is not used during model design and tuning

### Step 3: Feature engineering
Build rolling daily features for clustering and residual logic.

Most likely candidate feature set:
- rolling 20-day volatility
- rolling 60-day volatility
- rolling correlation to SPY
- relative return versus SPY over selected windows
- rolling drawdown
- possibly rolling beta to SPY

Important note:
The feature set should stay compact and interpretable. The assignment allows indicator freedom, but overloading the model with too many features risks data snooping and weakens the presentation narrative.

### Step 4: Monthly clustering
At each month-end:
- use only data available up to that date
- standardize features
- cluster the ETFs using the selected clustering method
- store cluster labels for that rebalance date

### Step 5: Build the cluster-relative residual
For each ETF:
- identify its cluster
- construct a cluster benchmark
- compute the ETF’s deviation from that benchmark

This residual becomes the raw mean-reversion signal.

### Step 6: Kalman smoothing
For each ETF or each cluster-relative residual series:
- apply a Kalman filter
- estimate the latent fair-value state
- compute the gap between observed residual and filtered residual

### Step 7: Trade signal generation
Possible first-version rules:
- if observed residual is sufficiently below the Kalman fair-value estimate, generate a long signal
- if observed residual is sufficiently above the Kalman fair-value estimate, generate a short signal
- if the deviation is too small or ambiguous, generate no trade

### Step 8: Portfolio construction
At each month-end:
- rank signal strength by absolute deviation or z-score-like standardized measure
- only hold the strongest opportunities
- create long, short, or neutral positions
- rebalance monthly

### Step 9: Backtest logic
- use training period for parameter selection and robustness checks
- use testing period only for final out-of-sample evaluation
- compare to SPY and possibly other neutral references where relevant
- assess not just cumulative return, but also drawdowns, turnover, hit rate, Omega or Sharpe, number of trades, and stability

---

## 10. Decision rules, first draft

These are the first explicit decision rules to carry forward.

1. Use only information available up to each month-end.
2. Compute rolling daily features for all sector ETFs.
3. Cluster ETFs into comparable groups using those features.
4. Define a cluster-relative benchmark for each ETF.
5. Compute the observed residual relative to that benchmark.
6. Apply Kalman filtering to the residual series.
7. If an ETF is materially below Kalman-implied fair value, it is a long candidate.
8. If an ETF is materially above Kalman-implied fair value, it is a short candidate.
9. If the signal is weak, unstable, or not sufficiently separated from noise, take no position.
10. Rank signal strength and only trade the strongest opportunities.
11. Rebalance monthly.
12. Evaluate the strategy on risk-adjusted and robustness metrics, not return alone.

These rules will later need to be translated into exact code logic and exact threshold parameters.

---

## 11. Evaluation philosophy

The strategy should not be judged solely on cumulative return.

Relevant evaluation dimensions:
- cumulative return
- drawdown size
- drawdown length
- Sharpe ratio
- Omega ratio
- hit rate or percentage win
- time in market
- number of trades
- turnover
- stability of results across nearby parameter values
- correlation to the underlying benchmark
- overall interpretability and robustness

Important class note:
A model that performs well only because of concentrated gains, excessive turnover, or fragile parameters may be overfit and should be treated with skepticism.

---

## 12. Bias control and common traps to avoid

This project should explicitly guard against the following.

### Look-ahead bias
Do not use information that would not have been available at the rebalance date.

### Data snooping
Do not test many unrelated ideas until one happens to work and then build the story afterward.

### Storytelling bias
Do not rationalize the model after seeing the backtest. The market view and mental model should be articulated first.

### Overfitting
Avoid choosing parameters only because they maximize the training result at one exact point. Prefer stable regions rather than cliff-like optima.

### Unrealistic implementation
Signals, trade timing, and return calculations should reflect a coherent execution assumption. The QT notes discuss the difference between returns depending on when the decision is made and when the trade is executed.

### Overcomplexity
This is a 10-minute presentation project with a small ETF universe. The model should remain explainable.

---

## 13. Why not the momentum route

Momentum was strongly considered. It likely fits the universe and monthly rebalance rule well. However, it was not chosen as the base project direction because:

- the assignment explicitly emphasizes clustering and Kalman
- in a momentum design, there was a risk that clustering would feel secondary and Kalman would feel like an add-on
- in a cluster-relative mean-reversion design, clustering defines the comparison set and Kalman smooths the central residual, so both required concepts are more naturally embedded
- the project is graded on implementation and defensibility rather than purely on historical alpha

This does not mean momentum is a bad strategy. It means cluster-relative mean reversion is a better fit for the assignment structure.

---

## 14. Initial design choices already made

These should be treated as current working decisions unless explicitly revised later.

### Fixed decisions
- Use the required SPY + sector ETF universe
- Use daily data
- Rebalance monthly
- Use R and Quarto only in the submission
- Follow the tidymodels workflow as much as practical
- Use clustering as the primary signal driver
- Use Kalman in conjunction with clustering
- Base the strategy on cluster-relative mean reversion
- Let the mental model drive the code
- Keep the strategy interpretable and presentation-friendly

### Strong current preferences
- Use a compact, interpretable feature set
- Focus on relative-value logic rather than pure directional calls
- Prefer robust parameter regions over hyper-optimized single points
- Use risk-adjusted and robustness metrics when comparing variants
- Treat the project as an implementation and reasoning exercise, not only a return-maximization contest

---

## 15. Important unresolved decisions

These decisions are not yet fully locked and will need to be finalized in the next stage.

### Clustering method
Not fully finalized.
Candidate options:
- hierarchical clustering
- k-means
- other simple clustering methods that remain interpretable

Current preference:
- hierarchical clustering is appealing because it is visually intuitive and presentation-friendly, but the exact choice still needs to be validated.

### Number of clusters
Not finalized.
Needs to balance:
- interpretability
- stability
- enough within-cluster comparability
- not over-fragmenting a small universe of ETFs

### Exact feature set
Not finalized, but likely to include some subset of:
- rolling volatility
- rolling correlation to SPY
- relative return versus SPY
- rolling drawdown
- rolling beta

### Definition of cluster benchmark
Not finalized.
Possibilities:
- simple cluster mean
- weighted cluster basket
- cluster centroid-like reference
- average standardized score

### Exact Kalman setup
Not finalized.
Need to choose:
- state-space specification
- whether to filter residual level only or residual plus drift
- exact implementation package or code path in R

### Signal threshold logic
Not finalized.
Need to define:
- what counts as "materially above" or "materially below"
- whether to use standard deviations, z-scores, absolute gaps, quantiles, or another threshold rule

### Portfolio construction specifics
Not finalized.
Need to choose:
- number of long and short positions
- weighting scheme
- whether to allow neutral periods
- whether to impose cluster diversification limits

### Execution assumption
Not fully finalized.
Need to make explicit:
- whether monthly signals are generated at close and executed at next open
- how returns are measured consistently from that point onward

---

## 16. Expected coding philosophy

When coding begins, the work should probably proceed in this sequence:

1. pull and clean data
2. split training and testing correctly
3. build rolling features
4. create monthly rebalance dates
5. test clustering logic in isolation
6. test residual construction in isolation
7. test Kalman smoothing in isolation
8. merge them into a complete signal pipeline
9. build monthly portfolio logic
10. evaluate and visualize results
11. refine only where the mental model supports refinement

This project should not begin by trying random indicators. It should begin by encoding the model that has already been justified.

---

## 17. Mermaid mental model status

A detailed prompt has already been prepared for Mermaid.ai to generate the diagram. The desired diagram should show:

- problem framing
- project constraints
- mental model toolbox
- market view
- daily inputs
- clustering as the primary signal engine
- cluster-relative residual construction
- Kalman filtering as smoothed state estimation
- explicit decision rules
- portfolio construction
- validation and iteration
- final strategic output

The final Mermaid diagram should communicate clearly that:
1. the strategy is cluster-relative mean reversion
2. clustering is primary
3. Kalman refines the cluster-relative signal
4. monthly trading is the end result of a structured process
5. the code must follow this exact logic

---

## 18. Full Mermaid.ai prompt

Use the following prompt in Mermaid.ai:

```text
Create a professional, presentation-quality mental model diagram for a quantitative trading project in finance.

The diagram should be clean, logical, and easy to present in class. It should look like a structured decision framework, not a decorative mind map. Use a top-down flow with clear layers, grouped boxes, and directional arrows. The style should feel like a serious finance or data science strategy map.

Title:
Mental Model Framework: Cluster-Relative Mean Reversion on S&P 500 Sector ETFs

Core purpose of the model:
Show how the project moves from problem framing, to market view, to clustering, to Kalman-filtered mean reversion signals, to monthly portfolio decisions, to validation and iteration.

The diagram must include these major sections in order:

1. Problem Framing
   - Real-world problem:
     "Can temporary divergences among sector ETFs be traded when sectors are first grouped into behaviorally similar clusters?"
   - Constraints:
     daily data
     monthly rebalancing
     SPY + Select Sector SPDR ETFs
     clustering must be primary
     Kalman filter must be used with clustering
     code must match the mental model

2. Mental Model Toolbox
   Show these as separate but connected mental models feeding into strategy design:
   - Systems Thinking
     "Sector ETFs are interconnected, not isolated"
   - Inversion
     "What would cause false signals or model failure?"
   - Margin of Safety
     "Only trade large enough deviations"
   - Risk-Reward Framework
     "Signal must justify risk, drawdown, and turnover"
   - Second-Order Thinking
     "Distinguish temporary dislocation from structural change"
   - Opportunity Cost
     "Only allocate capital to strongest signals"

3. Market View / Economic Logic
   - Sector ETFs sometimes form comparable behavioral groups
   - Within clusters, one ETF can temporarily overshoot or undershoot peers
   - Some deviations are noise, but some represent tradable dislocation
   - Mean reversion should be defined relative to cluster fair value, not the whole universe

4. Data and Inputs
   Use a grouped box labeled "Daily Inputs"
   Include:
   - daily adjusted prices
   - daily returns
   - rolling volatility
   - rolling correlation to SPY
   - rolling relative returns vs SPY
   - rolling drawdown or beta
   Show that all of these feed into clustering features and residual construction

5. Clustering Layer
   This must be visually emphasized as the PRIMARY method
   Label it:
   "Primary Signal Engine: Clustering"
   Show that ETFs are grouped into behaviorally similar clusters using rolling daily features
   Explain that clustering defines comparability and determines the reference group for fair value
   Add a note:
   "Fair value is cluster-relative, not global"

6. Residual / Mispricing Layer
   Show that after clustering, each ETF is compared against its cluster benchmark
   Include:
   - cluster average
   - cluster centroid
   - or cluster basket
   Then calculate:
   "Observed residual = ETF behavior minus cluster fair-value benchmark"

7. Kalman Filter Layer
   This must be clearly shown as working in conjunction with clustering
   Label it:
   "Kalman Filter: Smoothed State Estimate"
   Show that the Kalman filter is applied to the cluster-relative residual series
   Purpose:
   - estimate latent equilibrium
   - smooth noise
   - improve signal reliability
   Add a note:
   "Kalman does not replace clustering. It refines the cluster-relative signal."

8. Decision Rules Layer
   Make this a clean decision tree with arrows
   Include:
   - If observed residual is materially below Kalman fair value -> Long signal
   - If observed residual is materially above Kalman fair value -> Short signal
   - If deviation is too small or ambiguous -> No trade
   - Rank by signal strength
   - Hold only strongest opportunities
   Add:
   "Portfolio rebalanced monthly"

9. Portfolio Construction Layer
   Include:
   - monthly position selection
   - long / short / neutral outcomes
   - risk control
   - limited number of trades
   - signal threshold as margin of safety

10. Validation and Iteration Layer
   Show a final section for:
   - training period optimization
   - separate testing period backtest
   - evaluate drawdown, turnover, Sharpe/Omega, hit rate, stability
   - check for look-ahead bias, data mining, storytelling bias, overfitting
   Then loop back to earlier boxes with an arrow labeled:
   "Reflect and refine"

11. Final Output
   End with a final summary box:
   "Trade temporary sector ETF dislocations only when clustering identifies a valid comparison group and the Kalman-filtered residual suggests meaningful mean reversion."

Important design instructions:
- Make the overall structure hierarchical and presentation-ready
- Use grouped containers or subgraphs for the sections
- Use arrows to show causal flow, not just association
- Highlight clustering and Kalman as the central required concepts
- Make decision rules explicit and easy to follow
- Avoid too much text in each box, but keep labels meaningful
- Use concise finance-oriented wording
- Prefer a flowchart layout with some feedback loops for iteration
- The visual should communicate that the model is first conceptual, then computational, then evaluative

Optional visual emphasis:
- Use one color family for problem framing and mental models
- Use another for market/data/model logic
- Use another for decisions and portfolio actions
- Use another for validation and iteration

The final chart should make it obvious that:
1. the strategy is cluster-relative mean reversion
2. clustering is the primary signal driver
3. Kalman refines the signal rather than replacing clustering
4. monthly trades are the end result of a structured process
5. the code must later mirror this exact logic
```

---

## 19. Suggested next steps for future chats

When continuing this project in a new chat, the next tasks should likely be:

1. finalize the clustering method
2. finalize the clustering features
3. finalize the cluster benchmark definition
4. choose the Kalman implementation approach in R
5. write exact monthly decision rules and signal thresholds
6. outline the Quarto report structure
7. begin coding data pull and preprocessing in R
8. keep checking that every coding choice maps back to the mental model

---

## 20. Short version for quickly orienting a new model

If a future chat needs the shortest possible project handoff, use this summary:

This is an individual R/Quarto quantitative trading final project using SPY and the sector SPDR ETFs. The rules require daily data, monthly rebalancing, clustering as the primary signal engine, and Kalman filtering in conjunction with clustering. The chosen strategy is cluster-relative mean reversion. The market view is that sector ETFs can form comparable behavioral clusters, and temporary deviations inside a cluster may revert. The model clusters sector ETFs using rolling daily features, computes a cluster-relative residual for each ETF, applies a Kalman filter to estimate a smoothed fair-value state, and trades only the strongest monthly long or short dislocations. The code must follow the mental model exactly. The mental model uses systems thinking, inversion, margin of safety, risk-reward thinking, second-order thinking, and opportunity cost. Current unresolved items are the specific clustering method, exact features, cluster benchmark definition, Kalman setup, signal thresholds, and final portfolio-construction details.

---
