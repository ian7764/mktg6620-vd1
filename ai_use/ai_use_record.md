# AI-Use Record

Tool used: Claude (Anthropic), September 22, 2026

## Prompts used

1. Uploaded the four assignment files (churn.csv, VD1_analysis.py, VD1_requirements.txt, VD1_STUDENT_PACK.html.)
2. Read the Case 1 handout and VD1_analysis.py. Use the local churn.csv. Run the compare stage with the supplied settings. Explain the data checks, the three partitions, and how each method makes predictions. Show the validation table. Do not run the final evaluation until I record my choice. Help me understand the output, but do not write my assessed explanations or decision memo.
3. Asked what I need to submit and how to create a GitHub repository.
4. Asked for simplified, step-by-step setup instructions and a folder structure.
5. Asked how to open Terminal in my project folder (several follow-ups on navigating to the folder).
6. Checked my Python version (3.13) and created a Python 3.11 conda environment, then installed the pinned requirements.
7. Attempt that did not work: running the compare stage failed with a SciPy ImportError on macOS. Asked for a fix; installed SciPy 1.11.4 through conda, which upgraded NumPy to 1.26.4. Reran compare successfully.
8. Recorded my validation choice (boosted trees) in analysis.md, then asked how to create the repo and upload my files; committed before running the final evaluation.
9. Ran the evaluate stage with --choice trees and asked what the output tables meant, including probability_groups.csv.
10. Asked when and where to write my answers. AI provided an analysis.md outline with the number tables filled in from the script output; I wrote all explanations.
11. Asked AI to check that analysis.md had saved correctly.


## What AI helped with
I used AI throughout the assignment to help me work through the data analysis and better understand what I was doing. I asked questions about the original dataset, the three models, and results such as AUC, the top-20% churn rate, probability groups, and value scenarios. I also used it to troubleshoot technical problems with my terminal, Conda environment, Python and SciPy versions, and pushing my work to GitHub. I asked a few smaller follow-up questions that were not important enough to include in the prompt list. Overall, AI helped me check my work and understand the analysis, but I used the results to make my own model choice and write my own interpretations.

## Two checks I personally understood
1. I understood how split_rows.csv showed that every customer was placed in only one group: training, validation, or final test. This helped me confirm that the same customers were not being reused across different parts of the analysis.

2. I understood that the AUC comparison used the same groups of customers for both methods, which made it a fair comparison. Boosted trees clearly performed better than the contract rule, but the results were too close to say that it definitely performed better than logistic regression.