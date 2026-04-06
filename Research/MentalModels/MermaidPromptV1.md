Create a professional, presentation-quality mental model diagram for a quantitative trading project in finance.

The diagram should be clean, logical, and easy to present in class. It should look like a structured decision framework, not a decorative mind map. Use a top-down flow with clear layers, grouped boxes, and directional arrows. The style should feel like a serious finance or data science strategy map.

Title:  
Mental Model Framework: Cluster-Relative Mean Reversion on S\&P 500 Sector ETFs

Core purpose of the model:  
Show how the project moves from problem framing, to market view, to clustering, to Kalman-filtered mean reversion signals, to monthly portfolio decisions, to validation and iteration.

The diagram must include these major sections in order:

1\. Problem Framing  
\- Real-world problem:  
"Can temporary divergences among sector ETFs be traded when sectors are first grouped into behaviorally similar clusters?"  
\- Constraints:  
daily data  
monthly rebalancing  
SPY \+ Select Sector SPDR ETFs  
clustering must be primary  
Kalman filter must be used with clustering  
code must match the mental model

2\. Mental Model Toolbox  
Show these as separate but connected mental models feeding into strategy design:  
\- Systems Thinking  
"Sector ETFs are interconnected, not isolated"  
\- Inversion  
"What would cause false signals or model failure?"  
\- Margin of Safety  
"Only trade large enough deviations"  
\- Risk-Reward Framework  
"Signal must justify risk, drawdown, and turnover"  
\- Second-Order Thinking  
"Distinguish temporary dislocation from structural change"  
\- Opportunity Cost  
"Only allocate capital to strongest signals"

3\. Market View / Economic Logic  
\- Sector ETFs sometimes form comparable behavioral groups  
\- Within clusters, one ETF can temporarily overshoot or undershoot peers  
\- Some deviations are noise, but some represent tradable dislocation  
\- Mean reversion should be defined relative to cluster fair value, not the whole universe

4\. Data and Inputs  
Use a grouped box labeled "Daily Inputs"  
Include:  
\- daily adjusted prices  
\- daily returns  
\- rolling volatility  
\- rolling correlation to SPY  
\- rolling relative returns vs SPY  
\- rolling drawdown or beta  
Show that all of these feed into clustering features and residual construction

5\. Clustering Layer  
This must be visually emphasized as the PRIMARY method  
Label it:  
"Primary Signal Engine: Clustering"  
Show that ETFs are grouped into behaviorally similar clusters using rolling daily features  
Explain that clustering defines comparability and determines the reference group for fair value  
Add a note:  
"Fair value is cluster-relative, not global"

6\. Residual / Mispricing Layer  
Show that after clustering, each ETF is compared against its cluster benchmark  
Include:  
\- cluster average  
\- cluster centroid  
\- or cluster basket  
Then calculate:  
"Observed residual \= ETF behavior minus cluster fair-value benchmark"

7\. Kalman Filter Layer  
This must be clearly shown as working in conjunction with clustering  
Label it:  
"Kalman Filter: Smoothed State Estimate"  
Show that the Kalman filter is applied to the cluster-relative residual series  
Purpose:  
\- estimate latent equilibrium  
\- smooth noise  
\- improve signal reliability  
Add a note:  
"Kalman does not replace clustering. It refines the cluster-relative signal."

8\. Decision Rules Layer  
Make this a clean decision tree with arrows  
Include:  
\- If observed residual is materially below Kalman fair value \-\> Long signal  
\- If observed residual is materially above Kalman fair value \-\> Short signal  
\- If deviation is too small or ambiguous \-\> No trade  
\- Rank by signal strength  
\- Hold only strongest opportunities  
Add:  
"Portfolio rebalanced monthly"

9\. Portfolio Construction Layer  
Include:  
\- monthly position selection  
\- long / short / neutral outcomes  
\- risk control  
\- limited number of trades  
\- signal threshold as margin of safety

10\. Validation and Iteration Layer  
Show a final section for:  
\- training period optimization  
\- separate testing period backtest  
\- evaluate drawdown, turnover, Sharpe/Omega, hit rate, stability  
\- check for look-ahead bias, data mining, storytelling bias, overfitting  
Then loop back to earlier boxes with an arrow labeled:  
"Reflect and refine"

11\. Final Output  
End with a final summary box:  
"Trade temporary sector ETF dislocations only when clustering identifies a valid comparison group and the Kalman-filtered residual suggests meaningful mean reversion."

Important design instructions:  
\- Make the overall structure hierarchical and presentation-ready  
\- Use grouped containers or subgraphs for the sections  
\- Use arrows to show causal flow, not just association  
\- Highlight clustering and Kalman as the central required concepts  
\- Make decision rules explicit and easy to follow  
\- Avoid too much text in each box, but keep labels meaningful  
\- Use concise finance-oriented wording  
\- Prefer a flowchart layout with some feedback loops for iteration  
\- The visual should communicate that the model is first conceptual, then computational, then evaluative

Optional visual emphasis:  
\- Use one color family for problem framing and mental models  
\- Use another for market/data/model logic  
\- Use another for decisions and portfolio actions  
\- Use another for validation and iteration

The final chart should make it obvious that:  
1\. the strategy is cluster-relative mean reversion  
2\. clustering is the primary signal driver  
3\. Kalman refines the signal rather than replacing clustering  
4\. monthly trades are the end result of a structured process  
5\. the code must later mirror this exact logic