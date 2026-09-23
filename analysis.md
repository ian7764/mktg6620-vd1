# Analysis (recorded September 22, 2026)

## Validation choice

Chosen method: trees

Reason: I am choosing the trees method because it has the highest validation AUC, as well as the highest top 20% churn rate. Its AUC was 0.846, which is the highest when compared to 0.838 for logistic regression and 0.743 for the contract method. This may indicate that the trees model was the best at ranking customers according to their likelihood of churning. Also, among all customers in the highest-risk 20%, the trees model had a churn rate of 0.651. This means that about 183/281 of the selected customers actually churned, compared to 172 for logistic and 117 for the contract method. I think by focusing on the trees method, it allows us to focus on the retention efforts on the most relevant customers with the strongest validation performance.