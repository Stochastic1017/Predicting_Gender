# Predicting Gender based on first names #

## Introduction ##

__Project Overview__

This project is dedicated to exploring and reporting on the use of supervised machine learning models to predict a person's gender based solely on their first name. It encompasses a range of methodologies, from classical machine learning techniques to more advanced artificial neural networks, aiming to identify the most effective model for gender prediction.

__Objectives__

* To Evaluate Different Models: We aim to compare the performance of traditional machine learning models against that of artificial neural networks in predicting gender.
* To Optimize Prediction Accuracy: By fine-tuning model parameters and experimenting with various feature engineering techniques, the project seeks to optimize the accuracy of predictions.
* To Provide a Comprehensive Analysis: The project will include detailed documentation of the experiments, including data preprocessing, model training, validation, and results analysis.

## About the Dataset ##
The Dataset combines raw counts for first names of male and female babies,
it consists information from the following:

* US: Baby Names from Social Security Applications – National Data, 1880 – 2019.
* UK: Baby Names in England and Wales Statistical bulletins, 2011 – 2018.
* Canada: British Columbia 100 Years of Popular Baby names, 1918 – 2018.
* Australia: Popular Baby Names, Attorney-General’s Department, 1944 – 2019.

Further details can be found [here](https://www.kaggle.com/datasets/gracehephzibahm/gender-by-name).

A snapshot of the dataset is also provided below:

<img src="https://github.com/Stochastic1017/Predicting-Gender/blob/main/Images/snapshot.png" width="300" height="300">

## Feature Engineering ##

We first removed all rows with labels **uni-sex**, as we were only concerned with binary targets **male** and **female**, which we labelled **0** and **1** respectively. Then, we mapped each name with ISO Latin 1 characters to the equivalent ASCII characters using the **unidecode** package (further details can be found [here](https://pypi.org/project/Unidecode)). This left us with 24,595 names to be used to train our data.

<img src="https://github.com/Stochastic1017/Predicting-Gender/blob/main/Images/Pi-chart_male_female.png" width="200" height="200">

## One-Hot Encoding Names ##
Consider a $62$ length vector of the following form: 
```math
\begin{pmatrix} \text{a, b, ..., z} & | & \text{A, B, ..., Z} & | \text{ ', , AE, ae, Th, th, @, -, *, `} \end{pmatrix}
```
Each letter of a name can be one-hot encoded in the vector above. In order to make our one-hot encoding **sci-kit** usable, we pad enough rows with zeros so that the total number of rows is equals the length of the largest name in the dataset. In our data, the largest name  is $21$ characters long, i.e., each name in our dataset is encoded as a $(21 \times 62)$ 2-dimensional array, which after flattening gives us a 1-dimensional vector of size $1302$. 

```python
extra_characters = [' ', "'", 'AE', 'ae', 'Th', 'th', '@', '-', '*', '`']
all_letters = list(string.ascii_letters) + extra_characters
n_letters = len(all_letters)

max_letters = len(max(df['Name'], key = len)) # Length of largest name in the dataset. 

# Find letter index from all_letters.
def letter_to_index(letter):
    return all_letters.index(letter)

# Turn a letter into a (1 x 62) numpy array.
def letter_to_numpy(letter):
    numpy_char = np.zeros(n_letters)
    numpy_char[letter_to_index(letter)] = 1
    return numpy_char

# Turn a name into a (max_letters, ) length numpy array of one-hot letters padded with zeros.
def name_to_numpy(line):
    numpy_name = np.zeros((max_letters, n_letters))
    for li, letter in enumerate(line):
        numpy_name[li][letter_to_index(letter)] = 1
    return numpy_name.flatten()
```

An interactive-2d PCA projection of all our one-hot encoded names can be found [here](https://htmlpreview.github.io/?https://github.com/Stochastic1017/Predicting-Gender/blob/main/Images/PCA-2d.html).

An interactive-3d PCA projection of all our one-hot encoded names can be found [here](https://htmlpreview.github.io/?https://github.com/Stochastic1017/Predicting-Gender/blob/main/Images/PCA-3d.html).

## Algorithm to Find the Best Classifier ##
We used **GridSearchCV** with multicore **n_jobs = 5** to find the best hyper-parameters for binary classification using Knn and Logistic Regression. We found that **metric = cosine** and **n_neighbors = 3** were the best hyper-parameters for knn, and **C = 360** was the best hyper-parameter for logistic regression. Comparing validation accuracy's for both those models, along with decision tree classifier with **criterion = entropy** we found that decision tree classifier had the best performance.

<img src="https://github.com/Stochastic1017/Predicting-Gender/blob/main/Images/Compare%20valid%20accuracies.png" width="200" height="200">

## Gradient Boosting ##
As the validation accuracy for all our models is ranged from 0.71 to 0.74, we decided to fit an ensemble classifier model to maximize accuracy. As decision trees had the highest validation accuracy, we decided to go for the Gradient Boosting classifying model as it is an ensemble model that fits boosted decision trees by minimizing error gradient. After brute-forcing, we found that **n_estimators = 10000** and **learning_rate = 1.0** had the best validation accuracy at about 0.794, which was considerably better than the previous three models.

## Model Performance Assessment ##
After plotting Accuracy, Precision, Recall, and Area under the ROC curve, we can see that only Decision Trees and Gradient Boosting models have all scores over the threshold 0.70. Additionally, Gradient Boosting out performs every model by a decent margin, thus making it the final model that we choose for predicting gender based on names.

<img src="https://github.com/Stochastic1017/Predicting-Gender/blob/main/Images/Tests.png" width="200" height="200">

## Conclusion ##
In order to predict gender based on names, we used the **behind the name** data-set, conducted rigorous feature engineering, one-hot encoding, and splitting to make the data-set usable in **scikit**. After running a grid search and plotting the Knn, Logistic Regression, and Decision Trees with the most optimal hyper-parameters, we found that Decision trees had the best validation performance. Following this, we used gradient boosting ensemble classifier with 10,000 boosting stages and learning rate 1.0 to fit the best model that outperforms the previous three models by a considerable margin. 

<img src="https://github.com/Stochastic1017/Predicting-Gender/blob/main/Images/Confusion_MAT.png" width="250" height="200"> | <img src="https://github.com/Stochastic1017/Predicting-Gender/blob/main/Images/AOC_Curve.png" width="250" height="200">

## Contribution and Collaboration ##

We are eager to expand the diversity and inclusiveness of our dataset by incorporating names from more ethnic backgrounds and regions. If you have access to such datasets or can contribute to the existing dataset, please feel free to share your resources or suggest improvements. Your contributions can help enhance the project's scope and accuracy. Please refer to the [contributing guidelines](https://opensource.guide/how-to-contribute/) for more details on how to get involved.
