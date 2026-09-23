# Analysis (recorded September 22, 2026)

## Validation choice

Chosen method: trees

Reason: I am choosing the trees method because it has the highest validation AUC, as well as the highest top 20% churn rate. Its AUC was 0.846, which is the highest when compared to 0.838 for logistic regression and 0.743 for the contract method. This may indicate that the trees model was the best at ranking customers according to their likelihood of churning. Also, among all customers in the highest-risk 20%, the trees model had a churn rate of 0.651. This means that about 183/281 of the selected customers actually churned, compared to 172 for logistic and 117 for the contract method. I think by focusing on the trees method, it allows us to focus on the retention efforts on the most relevant customers with the strongest validation performance.


## Q1 — Generate

The contract rule groups customers by what kind of contract they have. This includes month-to-month, one year, or two years. It takes the type of contract then calculates a historical churn rate for each group. Every customer with the same contract type receives the same predicted churn probability, which makes the data easy to understand, but also happens to ignore other differences between the customers in the same group. The logistic regression uses all seven inputs in order to estimate each customer's probability of churning. All of the inputs that are numeric are standardized, and the categorical inputs are converted to indicator variables. This model will then weights all inputs and run a regression to account for all of these customer characterists at once. This is good, but it tends to assume that each characteristics' effects combine in a consistent manner. The boosted trees method also uses all seven inputs, but it creates a sequence of small decision trees in the model. Each of these decision trees tries to improve errors that may have been made by the previous trees. This tends to allow the model to identify these interactions and patterns in the data, and make a better decision around it. For example, the effect of monthly charges may differ depending on a customer's tenure or type of contract. This additional consideration might be why the boosted trees had the highest validation AUC and top-20% churn rate out of these three model types.

The seven inputs shared by the logistic regression and boosted-trees models are: 'tenure', 'MonthlyCharges', 'TotalCharges', 'Contract', 'InternetService', 'PaperlessBilling', "PaymentMethod'. 'customerID' is not used as an input because it is only a unique identifier. It does not describe any customer behavior, and using it could cause a model to memorize individual customers instead of learning patterns that generalize to other customers. 'Churn' is not used either because it is the outcome the methods are trying to predict. I think that including it might just cause leakage and produce misleading results, because these models would already have access to the answer it was looking for.

I ran the analysis in the `vd1` Conda environment using Python 3.11.16, pandas 2.0.0, scikit-learn 1.2.2, NumPy 1.26.4, and SciPy 1.11.4. The provided requirements pinned NumPy 1.24.2, but that version failed in my Mac environment, so I used NumPy 1.26.4 and documented the difference.

I first ran the comparison stage with: '`python VD1_analysis.py compare --csv churn.csv --out outputs`
After reviewing only the validation results, I recorded boosted trees as my choice. I then ran the final evaluation with:
`python VD1_analysis.py evaluate --csv churn.csv --out outputs --choice trees`

I used AI to help throughout the coding process, which led to displaying the AUC and top-20% churn-rate results, calculate the approximate numbers of churners identified by each method, and organize and revise the data. The supplied Python program performed the data processing, model fitting, predictions, and evaluation calculations with use of AI as well throughout.


## Q2 — Validate the analysis

### Data checks
The dataset contains 7,043 customers: 5,174 did not churn and 1,869 did. 11 customers had blank 'TotalCharges' values, and all had a tenure of zero months. The program reasonably replaced these blanks with zero because the customers had not accumulated charges yet. No rows were removed.

### Separate partitions and training-only fitting

The data was divided into 4,225 training records, 1,409 validation records, and 1,409 final-test records, representing a 60/20/20 split. The split was stratified so that each partition had a similar proportion of churners. The `split_rows.csv` file assigns every original row to exactly one partition, confirming that no customer appears in more than one group.

All three methods were fitted using only the training records. This restriction appears in the `fit_methods` function in `VD1_analysis.py`: the contract rates are calculated from `y[train]` and the training-row `Contract` values, while the logistic regression and boosted-trees pipelines are fit using `df.iloc[train]` and `y[train]`. The validation data was used to compare the methods and select boosted trees. The final-test data was not used until after I recorded that choice, reducing the risk of data leakage and overly optimistic results.


### Final-test ranking and contact list

| Method | Test AUC | Top-20% churn rate | Overall test churn rate |
|---|---|---|---|
| Contract rule | 0.737 | 39.9% | 26.5% |
| Logistic regression | 0.847 | 69.4% | 26.5% |
| Boosted trees | 0.850 | 69.4% | 26.5% |

The top-20% churn rate directly answers Devon's contact-list question because it shows the percentage of customers on each method's contact list who actually churned. Each list contained the 281 customers with the highest predicted risk. Approximately 112 of the customers selected by the contract rule churned, compared with about 195 selected by both logistic regression and boosted trees. The boosted-trees contact list had a 69.4% churn rate, about 2.6 times the overall test rate of 26.5%. This means the method successfully concentrated likely churners in the group targeted for retention efforts. Boosted trees also had the highest test AUC at 0.850, although logistic regression was very close at 0.847 and produced the same top 20% churn rate. Overall, the final-test results support the decision to use boosted trees while showing that logistic regression performed almost as well.

## Q3 — Uncertainty and value

### AUC intervals

| Comparison | Estimate | 95% interval |
|---|---|---|
| Contract rule AUC | 0.737 | 0.716 to 0.756 |
| Logistic regression AUC | 0.847 | 0.825 to 0.870 |
| Boosted trees AUC | 0.850 | 0.828 to 0.873 |
| Logistic minus contract | 0.110 | 0.093 to 0.129 |
| Trees minus contract | 0.112 | 0.096 to 0.131 |
| Trees minus logistic | 0.002 | -0.005 to 0.010 |

“Paired” means the methods are compared using the same resampled test customers, making the comparison fair. The code created the intervals from 1,000 bootstrap samples of the test customers, sampled with replacement, and kept the middle 95% of the results. Boosted trees had a test AUC of 0.850, with a 95% interval from 0.828 to 0.873. Its advantage over the contract rule was 0.112, with an interval from 0.096 to 0.131. Because this interval stays above zero, boosted trees clearly ranked customers better than the contract rule. However, the difference between boosted trees and logistic regression ranged from −0.005 to 0.010. Since this interval includes zero, neither method clearly outperformed the other. These intervals only measure uncertainty in the AUC results for this test sample while keeping the fitted models fixed. They do not cover uncertainty in the top-20% churn rate, campaign costs, customer responses, financial value, model refitting, or future changes in customer behavior.


### Probability accuracy (boosted trees)

| Group | n | Mean predicted | Observed churn |
|---|---|---|---|
| Overall | 1,409 | 27.0% | 26.5% |
| Top-20% list | 281 | 64.8% | 69.4% |
| 0.0 to 0.2 | 724 | 8.0% | 7.2% |
| 0.2 to 0.4 | 272 | 29.8% | 25.4% |
| 0.4 to 0.6 | 236 | 49.6% | 52.1% |
| 0.6 to 0.8 | 142 | 67.7% | 69.0% |
| 0.8 to 1.0 | 35 | 83.4% | 91.4% |

Overall, the boosted-trees probabilities were fairly close to the observed churn rates. The model predicted 27.0% churn overall, compared with 26.5% observed. For the top-20% list, it predicted 64.8%, while 69.4% actually churned, so it slightly underestimated risk in the contact group. The largest difference was in the 0.8-1.0 group, where predicted churn was 83.4% and observed churn was 91.4%. However, this group had only 35 customers, making its result less reliable. The 0.2-0.4 group also showed some mismatch, with 29.8% predicted versus 25.4% observed. Overall, observed churn generally increased with predicted risk, which suggests that the probabilities were reasonably accurate.


### Value scenarios (per 1,000 contacts, hypothetical)

| Method | List churn rate (r) | s = 10% | s = 15% | s = 20% | Break-even save rate |
|---|---|---|---|---|---|
| Contract rule | 39.9% | -$3,569 | -$2,254 | -$939 | 23.6% |
| Logistic regression | 69.4% | -$1,620 | $670 | $2,960 | 13.5% |
| Boosted trees | 69.4% | -$1,620 | $670 | $2,960 | 13.5% |

For example, the boosted-trees result at a 15% save rate can be checked as: 1,000 x (0.69395 x 0.15 x $66 - $6.20) = approximately $670.
At a 10% save rate, all three methods lose money. At 15% and 20%, logistic regression and boosted trees produce positive estimated values of about $670 and $2,960 per 1,000 contacts. The contract rule remains unprofitable because its 23.6% break-even save rate is higher than all three assumed rates. Logistic regression and boosted trees break even at about 13.5%. Changing the save rate changes whether the campaign is profitable, but it does not change my recommendation of boosted trees. Boosted trees and logistic regression have the same estimated value because their test contact lists have the same churn rate, while boosted trees had a slightly higher AUC. However, these estimates are hypothetical. They depend on sampling error in the contact-list churn rate, whether future customers behave similarily, and the unknown effect of the retention offer.


## Q4 — Explain your choice


| Method | Final-test AUC | ΔAUC vs. contract | 95% interval | Carry forward? | Reason |
|---|---|---|---|---|---|
| Contract rule | 0.737 | — | 0.716 to 0.756 (AUC) | Benchmark | Simple baseline, but weaker performance |
| Logistic regression | 0.847 | 0.110 | 0.093 to 0.129 | Strong alternative | Nearly matched boosted trees and is easier to explain |
| Boosted trees | 0.850 | 0.112 | 0.096 to 0.131 | Pilot candidate | Highest AUC and strong contact-list performance |

"Carry forward" describes the role of each method in the next stage. Boosted trees would be tested as the pilot candidate, the contract rule would remain in a basic benchmark, and logistic regression would be kept as a strong alternative.

I would change my recommendation if a larger test or live pilot showed that logistic regression produced better contact-list results or greater financial value. I would also reconsider the campaign if the actual save rate were below the 13.5% break-even point or if customer behavior changed enough to reduce the model's performance.


## Data source
Telco customer-churn example, scikit-learn/churn-prediction dataset mirror, CC BY 4.0 (checked September 9, 2026). Instructor-supplied copy used.