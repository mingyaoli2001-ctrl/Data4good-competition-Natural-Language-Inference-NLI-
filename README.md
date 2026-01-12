# Data4Good Case Challenge


## 📖 Background
Artificial Intelligence (AI) is rapidly transforming education by providing students with instant access to information and adaptive learning tools. Still, it also introduces significant risks, such as the spread of misinformation and fabricated content. Research indicates that large language models (LLMs) often confidently generate factually incorrect or “hallucinated” responses, which can mislead learners and erode trust in digital learning platforms. 

The 4th Annual Data4Good Competition challenges participants to develop innovative analytics solutions to detect and improve factuality in AI-generated educational content, ensuring that AI advances knowledge rather than confusion.

## 💾 The data

The data provided is a Questions/Answer dataset to determine if the answer is factual, not factual (contradiction), or irrelevant to the question.


- Question: The question asked/prompted for
- Context: Relevant contextual support for the question
- Answer: The answer provided by an AI
- Type:  A categorical variable with three possible levels – Factual, Contradiction, Irrelevant:
  - Factual: the answer is correct
  - Contradiction: the answer is incorrect
  - Irrelevant: the answer has nothing to do with the question
  
There are 21,021 examples in the dataset (`data/train.json`) that you will experiment with. 


The test dataset (`data/test.json`) contains 2000 examples that you predict as one of the three provided classes. In addition to classification performance we are seeking as detailed as possible methodologies of your step-by-step approach in your notebooks. Discuss what worked well, what did not work well, and your suggestions or ideas if a general approach to these types of problems might exist.

## 💪 Competition challenge

Create a report here in DataLab that covers the following:
1. Your EDA and machine learning process using the `data/train.json` file.
2. Complete the `data/test.json` file by predicting the `type` of answer for each question (2000 total). The `data/test.json` file also has an `ID` column, which uniquely identifies each row.



# Solution 1: Hugging Face  
Hugging faceAPI is a pre-trained AI models (DistilBERT), it understands the order of the words changes the meaning, allowing it to answer complex factual question where subject and object matter. After validation by extrating 15% of the data from the training data, it achieves a 93.8 Weighted Accuracy (To calculate the 93.8% weighted score, we take the accuracy of each individual class (Factual, Contradiction, Irrelevant) and average them together to ensure every category has equal importance)

## Technique
Instead of manually coding rules (for example: count the word 'not'), we allowed the model to learn the semantic relationship between the question, context, and answer through full -sentence attention mechanisms.

## What went well
In the previous iteration, I have uses XGBOOST to conduct deep learning which relied on word overlap. It often failed at identifying irrelevant sentence shared keywords with the question (like matching 'blue' in two unrelated sentences). Now, the transfor is able to understand context. From our validation result, we can observe that our model correctly identified 98.9% of irrelevant pairs, realizing that matching words do not always mean a matching answer. In terms of contradiction logic, my previous attempt is to count negative words ('not', 'never'), but when it comes to antonyms (good vs bad) it failed to identify the contradiction. Now, the Hugging face library is used to sucessfully detect the conflict bewteen question and answer (hot vs cold). 

## What Was Tried

| Phase | Approach | Performance | Verdict |
| :--- | :--- | :--- | :--- |
| **Phase 1** | **XGBoost (Feature Engineering)** | **83.6%** | **Good Baseline.** We manually engineered features like "negation count" and "word overlap." It was fast and efficient but hit a "glass ceiling" because it couldn't understand nuance or distinct meanings of similar words. |
| **Phase 2** | **Hugging Face (Deep Learning)** | **93.8%** | **Superior.** We utilized a pre-trained "brain" (DistilBERT). Although it required GPU resources and internet access to download weights, it outperformed the manual engineering approach in every category by "reading" the text rather than just counting it. |


## 🏆 Confusion Matrix: FINAL WEIGHTED SCORE: 93.81% (85/15) training vs validation

| True \ Predicted | Factual | Contradiction | Irrelevant |
| :--- | :---: | :---: | :---: |
| **Factual** | 2590 | 23 | 2 | 
| **Contradiction** | 44 | 228 | 1 |
| **Irrelevant** | 3 | 0 | 263 |


<img width="797" height="577" alt="image" src="https://github.com/user-attachments/assets/b85f6929-12ac-4531-8e9e-be7bcd95d0f6" />


# Solution 2: Using XGBOOST ⛹
> eXtreme Gradient Boosting is a software library that uses gradient boosted decision tree. It is a ML algo that builds a series of small decision trees where each tree fixes the errors of the one before it. In our case where the dataset is small, it is great at handle empty dataset automatically

My internet is wierd and couldn't make sucessfull connection to access hugging face library so XGBOOST is choosen to build the features myself (like lemmatization and negation counts)

### 1/9 result Old Confusion Matrix: FINAL WEIGHTED SCORE: 77.10%

| True \ Predicted | Factual | Contradiction | Irrelevant |
| :--- | :---: | :---: | :---: |
| **Factual** | 2457 | 121 | 37 |
| **Contradiction** | 106 | 142 | 25 |
| **Irrelevant** | 27 | 12 | 227 |

<img width="703" height="561" alt="image" src="https://github.com/user-attachments/assets/75a7c6de-d919-4d5e-a541-2fd4e392d530" />


# Solution 3: Optimized XBOOST ⛹
- decrease learning_rate -> model learns slower but finds more subtle patterns
- increase max_depth -> allows teh model to learn complex logic better
- increase n_estimator -> more trees, better result
- decrease max_features -> redice the width of the matrix so RAM doesn't explode

1/10 result
🏆 

### New Confusion Matrix: FINAL WEIGHTED SCORE: 83.62%

| True \ Predicted | Factual | Contradiction | Irrelevant |
| :--- | :---: | :---: | :---: |
| **Factual** | 2546 | 38 | 31 |
| **Contradiction** | 81 | 182 | 10 |
| **Irrelevant** | 30 | 5 | 231 |

<img width="864" height="666" alt="image" src="https://github.com/user-attachments/assets/1da81b88-f1e5-4d95-9e86-a9a084b9de63" />


