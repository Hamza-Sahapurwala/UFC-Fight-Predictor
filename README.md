# UFC Fight Outcome Prediction (UE24CS352A – ML Mini-Project)

Predicts a UFC fight as **red win / blue win / draw** from each fighter's career history before the fight.
Based on *"Applying Machine Learning Algorithms to Predict UFC Fight Outcomes"* (McQuaide).

## Setup
```bash
pip install -r requirements.txt
```
Place `datasetfromtheactualresearchpaper.csv` in the same folder as the notebook, then:
```bash
jupyter notebook UFC_Fight_Prediction.ipynb     # Run All (~1-2 min)
```
Running the notebook creates `ufc_model_dataset.csv` (engineered features) and `ufc_model.joblib` (trained model + fighter profiles).

## Predict a fight
```python
predict_fight("Donald Cerrone", "Neil Magny")                      # red corner first
predict_fight("Neil Magny", "Donald Cerrone", fight_date="2019-03-02",
              red_overrides={"age": 33, "ufc_prev": 20})           # optional overrides
```
Only fighters present in the dataset can be predicted (their history is needed).

## Work split
| Part | Owner | Content |
|---|---|---|
| Part 1 | Member 1 | Data audit, cleaning (dates, no-contests, missing values), EDA, leak-free feature engineering, leakage test |
| Part 2 | Member 2 | Time-based split, 6 models with time-series CV tuning, evaluation, final model, `predict_fight()` |

## Key decisions
* Raw file = post-fight, round-level stats -> converted to **pre-fight career averages** using earlier fights only.
* `Date` column is mixed-format and ~22 % of row dates contradict their event -> event-consensus date is used.
* Time-ordered split (train on older 80 %, test on newest 20 %); model chosen by CV on train only.
* Models: SGD/Perceptron, MLP, Decision Tree, Gradient Boosting (paper) + Logistic Regression, Random Forest.
