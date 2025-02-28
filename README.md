## Abstract

Notebook: https://colab.research.google.com/drive/18eC4kIxlNfqhwCORhM1Qsca9e1E-A3tj?usp=sharing

Dataset: https://www.kaggle.com/datasets/asinow/schizohealth-dataset

It will be a model-based agent. Our probabilistic agent will decide whether a patient should be put on suicide watch based on the probability that they will attempt suicide given their medical history. Specifically, if P(Y = 1 | X_1, X_2, ..., X_n) > 60%, the agent will decide to put the patient on suicide watch.

### PEAS
 - Performance Measure: prediction accuracy based on historical data.
 - Environment: a hospital, or psychiatrist/doctor’s office
 - Actuators: decision of committing a patient to suicide watch based on likelihood of a suicide attempt.
Likely: if P(suicide attempt) > 60%, then: commit patient
 - Sensors: Schizophrenia diagnosis, age, gender, income level, employment status, disease duration, number of hospitalizations and substance use.

### Probabilistic model

Our agent is set up with a Noisy-OR probabilistic model. Dependencies are set as followed:
   
![image1 (1)](https://github.com/user-attachments/assets/4f718e4b-6895-4e55-8e73-4bef5070c27c)

The variables we're considernig are as follows:
 - Age - patient's age between 18 and 80 years old.
 - Gender - 0: Female, 1: Male.
 - Education level - 1: Primary, 2: Middle School, 3: High School, 4: University, 5: Postgraduate.
 - Marital status - 0: Single, 1: Married, 2: Divorced, 3: Widowed.
 - Ocuppation - 0: Unemployed, 1: Employed, 2: Retired, 3: Student.
 - Income level - 0: Low, 1: Medium, 2: High.
 - Living area - 0: Rural, 1: Urban.
 - Diagnosis - 0: Not schizophrenic, 1: Schizophrenic.
 - Disease duration: Duration of illness for schizophrenia patients (1-40 years).
 - Substance use - 0: No, 1: Yes (Tobacco, alcohol, or other substances).
 - Hospitalizations - Number of hospital admissions (ranges from 0 to 10 for schizophrenia patients).
 - Family history -  0: No, 1: Yes (Genetic predisposition).
 - Stress factors - 0: Low, 1: Medium, 2: High.
 - Social support - 0: Low, 1: Medium, 2: High.
 - Medication adherence - 0: Poor, 1: Moderate, 2: Good.

## Description of dataset
This dataset is a collection of demographic, clinical and psychosocial history information from schizophrenia patients. We decided to use our probabilistic agent to analyze how the probability of the 'Suicide Attempt' feature is affected by the rest of the features in the dataset ('Diagnosis', 'Gender', 'Income level', etc.).

## Methods

We used the EM algorithm that's in HW3 to calculate the independent probability  p_i  for each feature x_i.

## Challenges
We had to divide certain features into categories to turn them into binary variables. Specifically, these features were age, education, marital status, occupation, years with the disease, number of hospitalizations, medication adherence, social support, level of stress.
Example: age ranges from 18-80 years old. We created three cause nodes for three age ranges: {[18-30], [30, 50], [50, 80]}.
Subsequently, we replaced the 'Age' column for these three columns where if the patient is 20 years old, 'age_18_30' = 1, 'age_31_50' = 0, and 'age_51_80' = 0.

![image0 (5)](https://github.com/user-attachments/assets/a3490f73-c448-49e8-b66f-98f89e441cf2)

### Code used to categorize features

<img width="318" alt="image" src="https://github.com/user-attachments/assets/27fc8094-a5de-4b3f-9574-0db5201473ac" /> <br>

<img width="420" alt="image" src="https://github.com/user-attachments/assets/02c43d4f-ad56-4072-9e1a-9fd4b5b02175" />

## Results

We created a test function that will get the probability of a suicide attempt based on a patient's history. <br>

<img width="542" alt="image" src="https://github.com/user-attachments/assets/1aa458a9-532b-430b-a076-8c1005739b5e" /> <br>

<img width="299" alt="image" src="https://github.com/user-attachments/assets/6e78a989-c2a3-45c7-8606-9364864102d8" /> <br>

Based on these tests, men with schizophrenia are more likely to attempt suicide than women. The diagnosis of schizophrenia also affects the likelihood severely (more than 3 times). <br>

We also testes based on marital status. <br> 

<img width="298" alt="image" src="https://github.com/user-attachments/assets/792f487b-467d-4ea4-b41d-f9e76372ef51" /> <br>

<img width="299" alt="image" src="https://github.com/user-attachments/assets/bae58eae-2667-4716-8ffa-e0c8f7442bd3" /> <br>

Based on these results, divorced men are the most likely to attempt suicide.








## What's next

- Maybe add weights to certain features depending on how much they affect the probability of a suicide attempt.
- Further categorize non-binary features.
- Make multiple noisy-ORs models for demographic features, medical history features, and psychosocial features.



