# UFC Fight Outcome Prediction

**Machine Learning Mini-Project · UE24CS352A**

A Python/Jupyter project that predicts the outcome of a UFC matchup as **Red-corner win**, **Blue-corner win**, or **Draw** using information derived from each fighter's previous UFC career history.

The project is based on *Applying Machine Learning Algorithms to Predict UFC Fight Outcomes* by McQuaide. Its main focus is building features that would be available **before** a fight, rather than allowing post-fight statistics to leak the answer into the model.

## What the project does

- Audits and cleans historical UFC fight data, including mixed-format and inconsistent event dates.
- Converts earlier fights into fighter-level career-history features.
- Avoids using the current fight's post-fight statistics as prediction inputs.
- Compares six machine-learning models using a chronological train/test split and time-series cross-validation.
- Saves a trained model and provides a `predict_fight()` function for trying matchups.

## Models and results

The notebook evaluates the following models on the newest 20% of fights, held out by date:

| Model | Test accuracy |
|---|---:|
| Logistic Regression | 58.5% |
| Random Forest | 57.5% |
| Gradient Boosting | 56.2% |
| Decision Tree (pruned) | 55.5% |
| MLP (L-BFGS) | 54.9% |
| SGD (Perceptron) | 51.0% |
| Always predict Red (baseline) | 55.5% |

**Selected model: Random Forest.** It was selected using time-series cross-validation on the training data, not by choosing the model with the highest test-set score. Its held-out test accuracy was **57.5%**, compared with **55.5%** for the simple always-Red baseline.

These results indicate only a modest predictive signal. The test set contains 463 fights, and the uncertainty around the selected model's accuracy includes the baseline. Treat the output as an educational experiment—not as reliable betting advice.

## Dataset

The notebook uses `datasetfromtheactualresearchpaper.csv`, sourced from this Kaggle notebook:

<https://www.kaggle.com/code/calmdownkarm/ufc-predictor-and-notes/notebook>

Download the CSV and place it at this exact path relative to the project root:

```text
Datasets/datasetfromtheactualresearchpaper.csv
```

The dataset is not assumed to be included in this repository. Check the source's terms before redistributing it.

## Project structure

```text
UFC-Fight-Predictor/
├── Datasets/
│   └── datasetfromtheactualresearchpaper.csv   # download separately
├── UFC_Fight_Prediction.ipynb                  # cleaning, EDA, training and prediction
├── README.md
└── requirements.txt                            # if included in the repository
```

Running the notebook also creates these files in the working directory:

- `ufc_model_dataset.csv` — engineered modeling dataset.
- `ufc_model.joblib` — saved model, feature definitions, and fighter profiles.

## Setup and run

### 1. Get the project files

Download or clone this repository, then open a terminal in the project root—the folder containing `UFC_Fight_Prediction.ipynb`.

### 2. Create and activate a virtual environment (recommended)

**Windows PowerShell:**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

If PowerShell blocks activation, you can continue using the environment's Python executable or use Command Prompt activation instead:

```bat
.venv\Scripts\activate.bat
```

### 3. Install dependencies

If the repository contains `requirements.txt`:

```bash
python -m pip install -r requirements.txt
```

If it does not, install the packages used by the notebook directly:

```bash
python -m pip install numpy pandas matplotlib scikit-learn joblib notebook
```

### 4. Add the dataset

Make sure the downloaded CSV is located at `Datasets/datasetfromtheactualresearchpaper.csv`. The folder name and file name must match the path used in the notebook.

### 5. Run the notebook

```bash
jupyter notebook UFC_Fight_Prediction.ipynb
```

In Jupyter, choose **Kernel → Restart Kernel and Run All Cells** (or **Run → Run All Cells**, depending on your version). Run the notebook from top to bottom. Its final interactive prediction cell asks for a Red-corner fighter and a Blue-corner fighter; enter the names when prompted. Model training and evaluation can take a couple of minutes, depending on your computer.

## Predict a matchup

After the notebook has executed the cells that define `predict_fight()`, try a matchup in a new code cell:

```python
predict_fight("Donald Cerrone", "Neil Magny")
```

The **first argument is the Red corner** and the second is the Blue corner. You can also supply a fight date and override selected fighter details:

```python
predict_fight(
    "Neil Magny",
    "Donald Cerrone",
    fight_date="2019-03-02",
    red_overrides={"age": 33, "ufc_prev": 20}
)
```

Only fighters present in the dataset can be predicted, because their historical profiles are built from that data. The function can attempt to match close spellings, but ambiguous or unavailable names may need correction. Any manual overrides should reflect the fighter information you want the model to use.

## Methodology notes

- **Three outcome classes:** `red`, `blue`, and `draw`. No-contest fights are removed because they do not have a winner.
- **Leakage-aware features:** current-fight stats are not used as pre-fight inputs. Historical fight statistics are aggregated from earlier fights only.
- **Date handling:** the notebook checks mixed-format dates and uses an event-consensus date when row-level dates conflict.
- **Chronological evaluation:** the older fights are used for training and time-series cross-validation; the newest 20% are held out for evaluation.
- **Missing data:** unknown values are not automatically treated as zero; model pipelines use median imputation where needed.
- **Rare draws:** draws are uncommon in this dataset, so the model has very little evidence from which to learn that class.

## Team contributions

| Part | Main work |
|---|---|
| Part 1 | Data audit and cleaning, exploratory data analysis, historical feature engineering, leakage checks |
| Part 2 | Chronological split, model comparison and tuning, evaluation, final model, `predict_fight()` |

## Limitations and future work

The dataset covers a limited historical period (2014–2018 in the evaluation notebook), fighter histories may be incomplete, and the source data has inconsistent dates and missing fight statistics. The model does not incorporate factors such as injuries, training-camp changes, reach, stance, rankings, or betting odds. Its results should not be generalized to current UFC matchups without updated data and fresh validation.

Possible extensions include adding more historical fights, incorporating reliable pre-fight attributes, calibrating predicted probabilities, and evaluating the system on a newer unseen period.
