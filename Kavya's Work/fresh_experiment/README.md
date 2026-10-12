# SC4000: NCAA tournament probability forecasting

A model for the Kaggle March Machine Learning Mania 2026 task: predict the probability that the lower-ID team wins each tournament game. The full implementation and evaluation are in [`SC4000_fresh_experiment.ipynb`](SC4000_fresh_experiment.ipynb); its outputs are in `results/independent/`.

## Approach

- **Separate men's and women's models.** Each is a regularised logistic regression, blended with a shallow boosted-tree model where earlier seasons favour it.
- **Features.** Opponent-adjusted margin ratings (full-season and recent form), Elo carried across seasons (75% of each rating into the next), schedule strength, win percentage, margin and seeds. The men's model also uses a consensus Massey computer ranking.
- **Training history.** Men from 2003, women from 1998 (the first seasons in which every feature exists).
- **Evaluation.** Nested rolling-origin over 2015–2025. Each season selects its settings on the five preceding tournament seasons and is fitted on earlier seasons only. Uncertainty comes from a game-level paired bootstrap.
- **Box scores** are excluded from the main model and kept only as an ablation.

## Results

On 1,315 tournament games (2015–2025), the model scores a Brier of **0.16303** (lower is better), against 0.16644 for the previous model on the same games. On 2023–2025 alone (402 games) it scores **0.15832**.

Differences are Brier for the variant minus the selected model, so positive means the variant is worse.

| Variant | Brier | Difference | 95% interval |
| --- | --- | --- | --- |
| Seed only | 0.17310 | +0.0101 | 0.0053 to 0.0149 |
| Without strength ratings | 0.16774 | +0.0047 | 0.0019 to 0.0075 |
| With box scores | 0.16478 | +0.0017 | −0.0006 to 0.0041 |
| Without Massey ranking (men only) | 0.16237 | −0.0007 | −0.0018 to 0.0004 |

Opponent-adjusted strength is the component with clear value. Neither the Massey ranking nor box scores helped.

## Caveats

- Box scores were dropped after the earlier version had been scored on 2023–2025, so those seasons are not fully untouched.
- The intervals ignore within-season correlation and should be read as approximate.
- No public Kaggle score has been obtained.
