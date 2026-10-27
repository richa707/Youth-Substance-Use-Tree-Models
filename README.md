# Youth Marijuana Use: Tree-Based Models on NSDUH 2023

Decision trees and ensemble methods applied to the 2023 National Survey on Drug Use and Health (NSDUH) to explore how family background and parental involvement relate to marijuana use among people under 18.

> Course project for DATA 5322, Seattle University, 2025.

## Research Question

Do family and socioeconomic background factors help predict whether a young person has used marijuana?

This is an exploratory, observational analysis. Results describe associations, not causes.

## Data

- **Source:** NSDUH 2023 (SAMHSA), pre-processed youth subset provided by the instructor.
- **Size:** 10,561 respondents under 18, 79 variables (numeric, ordered, and unordered categorical).
- **Outcome in the binary task:** `MRJFLAG` (ever used marijuana). About 50.9% of respondents have used it, so a majority-class guess scores about 51%.
- **Predictors in the binary tree:** parental involvement and monitoring (checking and helping with homework, limits on TV and time out, chores and allowance, parental praise and pride), arguments with parents, parents' marijuana-related variables (`PRMJEVR2`, `PRMJMO`), and whether a mother or father lives in the household.
- The data file is not included in this repo.

**Cleaning decisions**
- Converted numeric codes to labeled factors, binary and ordered where appropriate.
- Removed rows with missing values for the bagging and boosting models.
- Selected variables relevant to the research question.

## Models

| Task | Target | Models |
|---|---|---|
| Binary classification | `MRJFLAG` (ever used marijuana) | Decision tree, cost-complexity pruning |
| Multi-class classification | [target and number of classes] | Decision tree, bagging / random forest |
| Regression | [target, e.g., `IRMJFY`] | Random forest, gradient boosting |

**Split:** [30% train / 70% test, confirm which is which].
**Tuning:** pruning for the binary tree, `mtry` and `ntree` for the multi-class ensemble, shrinkage (0.001 to 0.3, five values) for boosting.
**Metrics:** accuracy for classification, MSE for regression.

## Key Findings

- **Binary:** pruning raised accuracy from 70.3% to 72.2%, against a ~51% majority-class baseline.
- **Multi-class:** hard. The base tree reached 25.25% accuracy, and the tuned ensemble (mtry = 5, ntree = 25) reached 29.46%.
- **Regression:** boosting performed best at a shrinkage of 0.1 [add MSE].
- **Predictors:** the pruned tree splits mainly on parental involvement, emotional support from parents, and household structure (e.g., a father in the household, feeling parents are proud).

## Results

### Binary classification

| Model | Accuracy |
|---|---|
| Majority-class baseline | ~50.9% |
| Decision tree (unpruned) | 70.3% |
| Decision tree (pruned) | **72.2%** |

### Multi-class classification

| Model | Accuracy |
|---|---|
| Decision tree | 25.25% |
| Bagging / random forest (mtry = 5, ntree = 25) | **29.46%** |

Accuracy is low for all models.

### Regression

| Model | MSE |
|---|---|
| Random forest | [ ] |
| Boosting (shrinkage = 0.001, ..., 0.3) | [ ] |
| Boosting, best (shrinkage = 0.1) | [ ] |

**Tracing one end node (node 42).** Starting at the root, a respondent reaches node 42 by: `PRMJEVR2` = Yes [confirm label] → `PRLMTTV2` = No [confirm label] → `PARLMTSN` = Yes → `IFATHER` = No (no father in household) → `IMOTHER` = Yes (mother in household). The node predicts marijuana use for 56.5% of respondents (deviance 84.92). In this group, a mother is present but a father is not, and parental limits are mixed. This is an association in survey
