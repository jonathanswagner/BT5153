# BT5153
Group project of BT5153 | Increasing Ad Effectiveness in Live Cricket

This notebook is leveraging several data sources that were either found on Kaggle or created from scratch using web scraping.

The scraping process is included in the notebook, however it is not recommended to rerun these portions of the code since it takes
a long time and might not be perfectly stable

## Pre-Processing
During the data pre-processing steps, the individual datasets in forms of csv files were strategically joint by using several keys such as player names and IDs or match IDs.

Several columns were in the form of strings so these either had to be converted to categorical variables or only the presence of certain keywords was converted into an indicator variable (e.g. bowling style in ln6)

With regards to data imputation, the main assumption is that players with no player stats are new and/or inexperienced players, hence their respective statistics were filled with the 25th percentile making them not the worst players on the field but definitely below average.

## Feature Engineering

During feature engineering using correlation plots, several high correlations between x variables were identified that are going to be addressed during the later modeling step. We want to avoid having multivariate correlation hence these correlated variables will be combined using PCA


Ln25 looks at the teams competing against each other and is binning the teams in 3 distinct tiers. The binning occurs based on domain expert judgement and is mostly related to the public perception of each team. This binning helps reducing the amount of variables since not every team needs to be OneHotEncoded during the modeling process and we end up with only 3 variables

Ln26 checks if one of the competing teams is playing in their home stadium. Due to the nature of the IPL, this is not guaranteed and only happens "by accident". The assumption is however, that teams playing at home might have an advantage due to better motivation and crowd support.

In order to bring together all features and transform every column with the respective transformer needed, the DataFrameMapper was chosen. The DataFrameMapper accepts a pandas dataframe as input and is able to apply different transformers to each feature as specified. It can then be combined with other FeatureTransformers together in a FeatureUnion

## Modelling

The first step in modelling is to prepare the y variable according to the business problem stated. This is a binary classification since we want to detect major events happening within a cricket match. Therefore, scores of 0-1 are encoded to 0 and every score above 1 are classified as a 1.

The data is then split into a training and test set. The final reporting of outcomes is done on the test set.

One of the most important steps is to define the model objective based on the business problem. Since the class imbalance between major and non-major event is extremely high, one cannot simply maximize classification accuracy since this would lead to the classifier only being able to detect the majority class. Therefore, as an objective for the GridSearch, the F1 score of the minority class was taken. Maximizing either precision or recall also leads in undesired outcomes since the model would also try to minimize classififcations for the minority group and hence bring down false positives by increasing false negatives. F1 seems to be a good middle-ground here.

As already mentioned, the class imbalance is extremely high so classifiers would not be able to learn anything. Therefore, sampling techniques need to be explored. Several techniques and combinations of techniques were explored using a logistic regression as baseline classifier. The most promising sampling happens with a combination of SMOTE and RandomUnderSampler. The corresponding sampling strategy will also be tuned in the following advanced classifiers

Following classifiers were trained and tuned with their respective parameters:
* Logistic Regression
* KNN
* Decision Tree
* LightGBM
* Random Forest
* Neural Network
* Random Guess

The random guess model is a random binomial distribution with the probability distribution obtained from the training data. Roughly 30% of the training data outcomes is a 1, hence this is the probability used for the random classifier as well.

The final model selection was not based on any model result such as accuracy, f1-score or precision but rather the direct applicaton on the business use case

The business use case is to maximize ad returns as much as possible which means, false positives for class 1 need to be minimized and true positives maximized as much as possible. The model that is able to do so, will generate the highest profit in excess of the random guess model.

From the results of cost_function() it can be seen that the second version of the LGBMClassifier was able to generate the highest excess profit using a classification threshold of 0.45. The excess profit on the training dataset is 5.82% per ad slot while the excess profit accounts to 2.88% on the test set previously created.

With an average cost of one ad slot of US$70.000, this accounts for roughly US$2.100 per ad slot that the company could be generating in additional profit and therefore increase the advertisement ROI above 0%
