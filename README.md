# Credit Line Increase Model Card

### Basic Information

* **Person or organization developing model**: Rose Hemans, `rose.hemans@gwu.edu`
* **Model date**: August, 2022
* **Model version**: 1.0
* **License**: MIT
* **Model implementation code**: [DNSC_6301_G46785210.ipynb](https://github.com/rosehemans/GWU_6301_rosehemans/blob/main/DNSC_6301_G46785210.ipynb)

### Intended Use
* **Primary intended uses**: This model is a graduate school project submission of an *example* probability of default classifier, with an *example* use case for determining eligibility for a credit line increase.
* **Primary intended users**: GWU Instructor for GWU DNSC 6301 Fall 2022 bootcamp.
* **Out-of-scope use cases**: Any use beyond grading a submission for the GWU DNSC 6301 final project is out-of-scope.

### Training Data

* Data dictionary: 

| Name | Modeling Role | Measurement Level| Description|
| ---- | ------------- | ---------------- | ---------- |
|**ID**| ID | int | unique row indentifier |
| **LIMIT_BAL** | input | float | amount of previously awarded credit |
| **SEX** | demographic information | int | 1 = male; 2 = female
| **RACE** | demographic information | int | 1 = hispanic; 2 = black; 3 = white; 4 = asian |
| **EDUCATION** | demographic information | int | 1 = graduate school; 2 = university; 3 = high school; 4 = others |
| **MARRIAGE** | demographic information | int | 1 = married; 2 = single; 3 = others |
| **AGE** | demographic information | int | age in years |
| **PAY_0, PAY_2 - PAY_6** | inputs | int | history of past payment; PAY_0 = the repayment status in September, 2005; PAY_2 = the repayment status in August, 2005; ...; PAY_6 = the repayment status in April, 2005. The measurement scale for the repayment status is: -1 = pay duly; 1 = payment delay for one month; 2 = payment delay for two months; ...; 8 = payment delay for eight months; 9 = payment delay for nine months and above |
| **BILL_AMT1 - BILL_AMT6** | inputs | float | amount of bill statement; BILL_AMNT1 = amount of bill statement in September, 2005; BILL_AMT2 = amount of bill statement in August, 2005; ...; BILL_AMT6 = amount of bill statement in April, 2005 |
| **PAY_AMT1 - PAY_AMT6** | inputs | float | amount of previous payment; PAY_AMT1 = amount paid in September, 2005; PAY_AMT2 = amount paid in August, 2005; ...; PAY_AMT6 = amount paid in April, 2005 |
| **DELINQ_NEXT**| target | int | whether a customer's next payment is delinquent (late), 1 = late; 0 = on-time |

* **Source of training data**: GWU Blackboard, email `rose.hemans@gwu.edu` or `jphall@gwu.edu` for more information
* **How training data was divided into training and validation data**: 50% training, 25% validation, 25% test
* **Number of rows in training and validation data**:
  * Training rows: 15,000
  * Validation rows: 7,500

### Test Data
* **Source of test data**: GWU Blackboard, email `rose.hemans@gwu.edu` or `jphall@gwu.edu` for more information
* **Number of rows in test data**: 7,500
* **State any differences in columns between training and test data**: None

### Model details
* **Columns used as inputs in the final model**: 'LIMIT_BAL',
       'PAY_0', 'PAY_2', 'PAY_3', 'PAY_4', 'PAY_5', 'PAY_6', 'BILL_AMT1',
       'BILL_AMT2', 'BILL_AMT3', 'BILL_AMT4', 'BILL_AMT5', 'BILL_AMT6',
       'PAY_AMT1', 'PAY_AMT2', 'PAY_AMT3', 'PAY_AMT4', 'PAY_AMT5', 'PAY_AMT6'
* **Column(s) used as target(s) in the final model**: 'DELINQ_NEXT'
* **Type of model**: Decision Tree 
* **Software used to implement the model**: Python, scikit-learn
* **Version of the modeling software**: Python version: 3.7.13, sklearn version: 1.0.2
* **Hyperparameters or other settings of your model**: 
```
DecisionTreeClassifier(ccp_alpha=0.0, class_weight=None, criterion='gini',
                       max_depth=6, max_features=None, max_leaf_nodes=None,
                       min_impurity_decrease=0.0, min_impurity_split=None,
                       min_samples_leaf=1, min_samples_split=2,
                       min_weight_fraction_leaf=0.0, presort='deprecated',
                       random_state=12345, splitter='best')
```
### Quantitative Analysis

| Tr AUC | Val AUC | Test AUC |
| ------ | ------- | -------- |
| 0.7837 | 0.7496  | 0.7438 |

| Val Hispanic-to-White AIR | Val Black-to-White AIR | Val Asian-to-White AIR | Val Female-to-Male AUC |
| ------------------------- | ---------------------- | ---------------------- | ---------------------- |
| 0.76                      | 0.82                   | 1.00                   | 1.06


#### Correlation Heatmap
Notable observations include relatively strong negative correlation between RACE (where 1 = hispanic; 2 = black; 3 = white; 4 = asian) and DELINQ_NEXT (whether a customer's next payment is deliquent). This indicates that, within this dataset, hispanic and black customers' next payment was more likely to be delinquent than white or asian customers.

PAY_0, PAY_2 - PAY_6 and DELINQ_NEXT exhibited an increasingly positive correlation by chronology of payments. This indicates the later a payment is made, the more likely a customer's next payment is delinquent. This becomes increasingly true for more recent payments i.e., there is a stronger positive correlation betweeen PAY_0 and DELINQ_NEXT than between PAY_6 and DELINQ_NEXT.

![Correlation Heatmap](https://github.com/rosehemans/GWU_6301_rosehemans/blob/main/DNSC%206301%20Pearson%20Correlation%20Matrix.png?raw=true)


#### Variable Importance
PAY_0 and PAY_2 are the two most important variables in the model. These calculations indicate that the model's performance is sensitive to major distribution changes of the PAY_0 variable.

![Variable Importance Chart](https://github.com/rosehemans/GWU_6301_rosehemans/blob/main/Variable%20Importance.png)


#### Final Iteration Plot
The iteration plot indicates that a tree depth of 7 for the model would provide the best trade-off between fairness and accuracy.

![Iteration Plot](https://github.com/rosehemans/GWU_6301_rosehemans/blob/main/final%20iteration%20plot.png)


### Ethical Considerations

We must consider the unstable nature of decision trees. As evidenced by the high variable importance of PAY_0, a disruptive change in the distribution of the data could result in a major change in the structure of the model. For example, a national or global recession could significantly affect customers' most recent payment ability. As a result, the performance of the model would become very unstable. 

Financial service providers, consumers, policymakers, and regulators all stand to be affected by potential negative impacts of this model. Credit lenders may be satisfied with this model but consumers will always demand more predictive, less biased models to maximize their chances of accessing a less biased, broader range of loan types at lower rates. Current legal and regulatory laws are inadequate for the application of machine learning and artificial intelligence. While this model has been built to reduce bias and maintain accuracy, without a standardized regulatory framework, models will continue to vary considerably from lender to lender. This model may fall short of the accuracy of others. 

There is a considerable degree of uncertainty regarding the use of variables that could be considered proxies for other variables. For example, previous pay amounts could be a proxy for income. Income gaps between races become introduced into the model even when we remove demographic variables. 
