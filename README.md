# MKTG 6620 – V&D Project 1: Which Customers Should the Retention Team Contact?

## Data
This project uses `churn.csv`, the Telco customer-churn example supplied by the instructor on Canvas (Modules – 3 – VD1_student_files.zip). The CSV is not included in this repository. Download it from Canvas and place it in this folder before running.

Source: Telco churn dataset, scikit-learn/churn-prediction dataset mirror, CC BY 4.0 (checked September 9, 2026).

## Setup
Python 3.11 in a conda environment:

    conda create -n vd1 python=3.11 -y
    conda activate vd1
    pip install -r VD1_requirements.txt
    conda install -y scipy=1.11

Note: the SciPy version pip installed alongside the pinned packages would not load on macOS, so SciPy 1.11.4 was installed through conda, which also upgraded NumPy to 1.26.4. Exact versions used are recorded in `outputs/compare_run.json` and `outputs/evaluate_run.json`.

## Commands
1. Compare the three methods on validation data:

       python VD1_analysis.py compare --csv churn.csv --out outputs

2. After recording the choice in `analysis.md`, evaluate on the final test set:

       python VD1_analysis.py evaluate --csv churn.csv --out outputs --choice trees

## Choice
Boosted trees (`trees`), selected on validation AUC and recorded before running the final evaluation.

## Files
- `analysis.md` – write-up (Q1–Q4)
- `VD1_analysis.py`, `VD1_requirements.txt` – supplied code and requirements (unchanged)
- `outputs/` – all script outputs
- `ai_use/` – AI-use record
- Memo PDF – one-page memo to Devon Achebe