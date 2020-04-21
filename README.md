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

One of the most important steps is to define the model objective based on the business problem. 
