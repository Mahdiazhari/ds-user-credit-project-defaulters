# User Credit Data: Segregating Customers into Defaulters vs Non-Defaulters to minimize credit risk

This project aims to analyze the credit card/borrowing behavior of Indonesian customers.

Please see the attached presentation slide or accompanying notebook titled "User Credit Data Project - Data Analysis" for detailed results regarding analysis of the Data.

## Introduction

As a lender/financial company providing loans, we have to be very selective in deciding our future borrowers/customers.
If lenders take in too many high risk borrowers, then negative effects for lenders include:

- Having to pay costs to recoup on loans
- Missing opportunity to lend the money elsewhere

For the borrowers/customers, defaulting on loans could lead them to:

- Get penalties for late payments
- Loss of collateral through debt collection to give back the lender's money
- Costs from legal action taken by lenders to get their money back

Therefore, we would really like to know the **characteristics** of borrowers who eventually cannot pay back their loans (Default on loan). And it would be very helpful to be able to somehow
segregate these two groups.

## Research Question

The aim of this project is to identify the characteristics of a customer/user who will eventually default on their loans. Thus, I aim to answer the following questions:

1. What are the characteristics of defaulters vs non defaulters?
2. Can we build a good classifier to classify our userbase into Default/Non-Default customers?

## Dataset

This is the description of all the features in the data:

- x : represents the credit user's ID
- number_of_cards: represents the number of credit cards owned by the user
- outstanding: represents the total outstanding amount of credit card usage of that user
- credit_limit: the credit limit amount that can be used
- bill: last month customer bill amount
- total_cash_usage: last month total cash usage of customer
- total_retail_usage: last month total retail usage of customer
- remaining_bill: remaining bill that has not been paid in the last month
- branch_code: the branch code of the customer (branch location where the customer's data was obtained)
- payment_ratio: payment per bill ratio in the last month
- overlimit_percentage: overlimit percentage
- payment_ratio_3month: payment per bill ratio in the last 3 month
- payment_ratio_6month: payment per bill ratio in the last 6 month
- delinquency_score: delinquency score
- years_since_card_issuing: total year since first card issued
- total_usage: total usage
- remaining_bill_per_number_of_cards: ratio remaining bill per number of cards
- remaining_bill_per_limit: ratio remaining bill per credit limit
- total_usage_per_limit ratio: total usage per limit
- total_3mo_usage_per_limit: the ratio of total 3 months usage per limit
- total_6mo_usage_per_limit: the ratio of total 6 months usage per limit
- utilization_3month: Credit card utilization for past 3 months
- utilization_6month: Credit card utilization for past 6 months
- default_flag: Credit default flag (1: default; 0: non_default)

Default refers to a user not being able to pay their debts on time.

## Exploratory Data Analysis Results

Reminder:
Please see the attached presentation slide or accompanying notebook titled "User Credit Data Project - Data Analysis" for detailed results regarding analysis of the Data.

**Overall statistics**:

- Most users have 2 credit cards, with those above 2 being very rare.
  What is interesting is the fact that the number of users with 1 credit card is very low.
  Perhaps users want as much credit cards as possible, to take advantage of promos and such. But they are limited by the BI regulation which states that:
  **users with income of under 10 million IDR should have only 2 credit cards maximum**.
- Overall, most of the data distribution tells us that the data is **skewed right**. This conforms with the usually observed patterns in real life e-commerce data, where a large number of users would have a very small amount of transactions on the platform, but a small amount of users would have a very high number of transactions on the platform.

**Categorical Variables**:

- Branch:
  - Out of all the branches, branch A has the most amount of credit card users with branch K having the least amount.
  - Branch G performs the best, with 2 % default rate
  - Branch E performs the worst, with 17% default rate
- Delinquency score:
  - Overall, most users have a good delinquency score, with a score of 0. Meaning, that they are more likely to pay their bills on time. Users with bad delinquency scores (above 0) are pretty rare.
  - Lenders should avoid giving credit to users with > 0 delinquency score.
  - Lenders should prioritize giving credit to users with 0 delinquency score
- Default flag:
  - Most users do not default on their credit card payment. They seem to be succesful at paying their credit cards by the due date.
  - Only a handful (8 percent) of users are defaulters.
  - The data is imbalanced, but in this case, it is a good thing. Since, if there are too many defaulters, then profit of the Lender might decrease because more money is lent
    without any return/payback from the customers.

**Defaulter Characteristics**:

- Defaulters tend to have a lower payment ratio
- Tend to use their credit card less than non-defaulters
- Same number of credit cards as non-defaulters
- Same credit limit as defaulters
- One group of defaulters cannot even pay a small amount of outstanding (I call this the very low income defaulter group)

## Model Development

### Baseline Result

This is a classification problem, as we need to identify which users belong to which group, from a total of 2 groups: 1. Defaulters (1) 2. Non-Defaulters (0)
Before I start, I define the baseline result here (taken from: https://machinelearningmastery.com/how-to-get-baseline-results-and-why-they-matter/):

Since the classes are imbalanced and has more observations for non-defaulters, we select the class that has the most observations and use that class as the result for all predictions.

I obtain a 92.65 % accuracy (classification score) baseline result.

### Random Forest Classifier

I selected Random Forest as my go-to algorithm to classify the data. It is also selected because of its `feature importances`. As one of the objectives of this analysis
is to find the difference between the two customer groups (Defaulter vs Non-Defaulter).

Two Random Forest models were created, one without parameter tuning, the other using Grid Search parameter tuning.

#### Features Importances

![Features Importances](/images/features-importances.png)

### Model/Solution Results

- 2 Random Forest Classification Models were made using base Random Forest (First model) and used Grid Search to tune params (Second Model):
  - The First model has better Recall and AUC score
  - The Second Model has better Accuracy, F1 score, and Precision
- The first model obtained an accuracy of 78%, the second model got 91%
- Both of these models could not beat the baseline accuracy of 92.65 %
- Total usage (both retail usage and total usage per limit in 3months) and payment ratio are the most important features to predict Default/Non-Default
- Select models based on recall score, This is because a high recall score would really help in selecting the actual amount of defaulters. False negatives are costly because we should not give credit to people who cannot pay back their loans
- Need to balance costs of false positives vs false negatives in the prediction model
