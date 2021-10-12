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

The only source of unaccounted missingess is the Amount of Deductable Paid ('DeductableAmtPaid'). There are 899 values, where there was no payment recorded (not \\$0). My interpretation is this is due to non-payment by the patient and was imputed to 0 for our analysis.


# Modeling


# Analysis


# Photo Credit
Photo by [Online Marketing](https://unsplash.com/@impulsq?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Upsplash](https://unsplash.com/s/photos/healthcare?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
  