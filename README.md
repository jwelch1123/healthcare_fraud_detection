# Healthcare Fraud Detection
Detecting potential fraud in Healthcare Providers by patient records. 

[Blog Post](https://nycdatascience.com/blog/author/james-welch/)
[Reference Kaggle Compeititon](https://www.kaggle.com/rohitrox/healthcare-provider-fraud-detection-analysis)

# Background & Problem

[Medicare](https://www.medicare.gov/what-medicare-covers/your-medicare-coverage-choices/whats-medicare) is the federal health insurance program primarily for the elderly and disabled (distinct from Medicaid). If people choose to forgo private insurance, they can enroll in medicare once they reach 65 years of age or under specific circumstances such as disability or Renal Disease. 

Generally there are two parts of Medicare in which patients are enrolled, PartA: Hospital Insurance (Inpatient) and Part B: Medical Insurance (Doctor's Services, outpatient care, preventive services). These can also be paired with Medicare D (prescription drug coverage) and/or private insurance (dental, eyes).

Since this system allows most doctors to treat patients, as opposed to having an approved list, there is a hightened chance of fraud. This can take multiple forms, such as: Upcoding, unbundling, kickbacks, preforming unnesseary services, or keeping a patient in the hospital longer than nessesary. It can also involve Doctors, Networks, whole Clinics, and even patients. Due to this being a federal program funded with tax-payer dollars, fraud threatens the whole system. Reducing the money available in the system and increasing premiums for everyone.

Given the amount of taxpayer dollars potentially being lost to fraud, detecting said fraud is of the utmost importance. Below I go into the data preparation methods and analysis used to accuratly identify fraudulant providers. The dataset provided is sourced from a [kaggle dataset](https://www.kaggle.com/rohitrox/healthcare-provider-fraud-detection-analysis) and contains seperate tables for Inpatient & Outpatient Claims, Beneficiary information such as location & chronic conditions, and a provider key indicating the prescense of fraud. 


# Data Cleaning & Engineering

Luckily this is a high-quality dataset, there appears to be only 1 column with missing values not accounted for by the sturcture of the dataset. Beneficiary data contains a 'DOD' (date of death) column which, luckily for everyone, only contains 1400 values. In the Inpatient and Outpatient data, the main sources of missingness are Diagnosis & Procedure Codes, and Physicians. In both cases, missingness indicates a less complicated claim. Fewer diagnosis codes lead to fewer treatments, and require fewer physicians. 

The only source of unaccounted missingess is the Amount of Deductable Paid ('DeductableAmtPaid'). There are 899 values, where there was no payment recorded (not \$0). My interpretation is this is due to non-payment by the patient and was imputed to 0 for our analysis.

Functions were created to clean the data. Removing date columns, imputing the missing values, dropping sensitive information, and recoding chronic conditions to 0/1 notation.

The data was further split into train and test fractions, preserving the abundance of each class in the splits.


# Upsampling

In order to balance our unbalanced dataset I implemented various SMOTE techniques. SMOTE: Synthetic Minority Over-sampling Technique, increases the number of the minority class by creating new datapoints 'between' existing data. Due to the prevalance of categorical data, I employed a categorical and contenous version of SMOTE, SMOTE-NC. Additionally, I used SMOTE-Tomek to remove conflicting datapoints and sharpen the decision boundry between classes.

# Modeling

Modeling took place in two distinct stages, first several models were assess without hyperparameter modification. Second, high preforming models were tuned via RandomSearch cross-validation.

The first pass of modeling used the four datasets generated via upsampling: original, SMOTE, SMOTE-NC, and SMOTE-Tomek. Four models were trained on each dataset: Random Forest, Gradient Boost, XGBoost, and CatBoost. All SMOTE methods imporve the accurarcy score over the original dataset, SMOTE-Tomek had a higher accuracy compared to the rest of the data, possiblity due to trimming possible conflicting datapoints, but was used in subsequent hyperparameter tuning.

XGBoost and Catboost hyperparameters were tuned via RandomizedSearchCV, scoring for recall instead of accuracy. Recall is used to reduce the False Positive rate which in our context equates to accusing a provider of fraud. After tuning CatBoost increase in accuarcy from 82\% to 85\%. 

# Analysis

The tuned CatBoost model has excellent train scores but only decent test scores, indicating moderate overfitting. The metrics for the Test data are:

|Metric|Value|
|------|------|
|Accuarcy| 78\%|
|Recall | 62\% |
|Precision|76\%|
|f1 | 68\%|

Permutation importance was used to evaluate the features of the model. State and County have very high scores, possibly indicating data leakage (many claims by the same provider in each state/county). Otherwise the number of diagnosis codes, physicans taking multiple roles, and several of the cummulative features are most important. 

# Aggregating Predictions

Normally we would stop there, we predicted claims to a 78\% accuary, however we are actually interested in Providers. Claims is just the data we are working with to find our answer. The probability of fraud for each claim is calculated and the Provider ID, actual class, and Claim End Date is added. This allows the aggregation of Providers, and the ordering of claims through time. 

New features are calculated from this data, tracking the order of claims and the accumulating mean of the fraud probability.

Using all the claims in our dataset, we can achieve the following metrics in our test split:

|Metric|Value|
|------|------|
|Accuarcy| 87\%|
|Recall | 46\% |
|Precision| 39\%|
|f1 | 42\%|

However we need not use all the data, we can achived nearly identical accuarcy and recall using fewer claims; 5 claims appears to be the best trade-off in terms of maintaining accuarcy and quickly identifying fraud. 

Examining the predicted probability of fraud for each claim by Provider, we can observe some distinct shapes and patterns. This might point to different 'systems of fraud' such as submitting mixs of real and fraudulent claims, fraudulent physicians in a larger network, or simply a missing dimension of our data which would push claim predictions toward either extreme. 

# Conclusion and Next Steps

Our system has an 87\% accuarcy at identifying the possibility of fraudulent providers though still has a low recall and precision score. Aggregating provider information reveals that we only need to have 5 claims to maintain our accuracy metrics. Due to fraudulent providers systematically making more claims, lowering the number of claims needed to render a decision increase the likelihood of identifying them. 

Getting claims specific fraud tags would be a extremely useful and allow us to glean more information from the claims data which in turn should increse the accuracy of identifying of fraudulent providers and their systems of fraud.


# Photo Credit
Photo by [Online Marketing](https://unsplash.com/@impulsq?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Upsplash](https://unsplash.com/s/photos/healthcare?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
  