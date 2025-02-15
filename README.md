# Abstract

Link to notebook: https://colab.research.google.com/drive/18eC4kIxlNfqhwCORhM1Qsca9e1E-A3tj?usp=sharing

Dataset: https://www.kaggle.com/datasets/asinow/schizohealth-dataset

Our probabilistic agent will decide whether a patient should be put on suicide watch based on the probability that they will attempt suicide given their medical history. It will be a model-based agent. Its PEAS are:
 - Performance Measure: prediction accuracy based on historical data.
 - Environment: a hospital, or psychiatrist/doctor’s office
 - Actuators: decision of committing a patient to suicide watch based on likelihood of a suicide attempt.
Likely: if P(suicide attempt) > 60%, then: commit patient
 - Sensors: Schizophrenia diagnosis, age, gender, income level, employment status, disease duration, number of hospitalizations and substance use.

   Our agent is set up with a Noisy-OR probabilistic model. Dependencies are set as followed:
![image1 (1)](https://github.com/user-attachments/assets/4f718e4b-6895-4e55-8e73-4bef5070c27c)


![image0 (5)](https://github.com/user-attachments/assets/a3490f73-c448-49e8-b66f-98f89e441cf2)

## Description of dataset
This dataset is a collection of demographic, clinical and psychosocial history information from schizophrenia patients. We decided to use our probabilistic agent to analyze how the probability of the 'Suicide Attempt' feature is affected by the rest of the features in the dataset ('Diagnosis', 'Gender', 'Income level', etc.).

## Methods
We used the EM algorithm that's in HW3 to calculate the independent probability  x_i values for each cause.

## Challenges
We had to divide certain features into categories to turn them into binary variables. Specifically, these features were age, education, marital status, occupation, years with the disease, number of hospitalizations, medication adherence, social support, level of stress.
Example: age ranges from 18-80 years old. We created three cause nodes for three age ranges: {[18-30], [30, 50], [50, 80]}.
Subsequently, we replaced the 'Age' column for these three columns where if the patient is 20 years old, 'age_18_30' = 1, 'age_31_50' = 0, and 'age_51_80' = 0.
<img width="318" alt="image" src="https://github.com/user-attachments/assets/27fc8094-a5de-4b3f-9574-0db5201473ac" />
<img width="420" alt="image" src="https://github.com/user-attachments/assets/02c43d4f-ad56-4072-9e1a-9fd4b5b02175" />

## Results

## What's next
Maybe add weights to certain features.
Further categorize non-binary features.
Make multiple noisy-ORs models for demographic features, medical history features, and psychosocial features.



