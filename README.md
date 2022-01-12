# 2020 Olympics Medal Winning Countries Classification

## Introduction

113 of the 206 countries that showed up to the 2020 Summer Olympics didn’t win a single medal. If the Olympics are structured such that the majority of countries won’t win any medals, how do these non-winning countries assess their performance? I set out to build a classification model that could predict whether a country won a medal at the 2020 Olympics based on national economic indicators. These predictions would let non-medal winning countries know if they were predicted to win a medal or not. Classification modeling would also provide probability scores for how likely a country was to win an Olympic medal. Together, these would provide additional context for non-winning countries as to how their performance stacked up to expectations.

I obtained the Olympic medal dataset by merging Olympic medal counts scraped from the Olympics and CBS Sports websites with economic indicator data from the Google BigQuery World Bank World Development Indicators SQL database. After scrubbing, the dataset included 193 datapoints across 9 features. I built a baseline Dummy Classifier model, 5 classification models and a Voting Classifier model. The top performing SVM/XGBoost Voting Classifier model (f1 Score: .80, Accuracy: .81) strongly outperformed the baseline Dummy Classifier model (f1 Score: .48, Accuracy: .45). Logistic Regression (f1 Score: .73, Accuracy: .71) feature coefficients revealed that the greatest positive drivers of model performance were Population (Coefficient: .81 ) and Urban Population Proportion (Coefficient: .70), while the most influential negative features were Migrant Population Proportion (Coefficient: -.33) and Ages 20–34 Population Proportion (Coefficient: -.43).


## Obtain Data

![CBS Sports Olympic medal count webpage](https://github.com/blantj/olympic_medal_regression/blob/main/Images/CBS_Sports.png)

I scraped a list of the 206 Olympic federations from the Olympics website. I next scraped medal counts for the 93 2020 Olympics medal winning countries from the CBS Sports website. I finally obtained economic indicators for each nation or territory from the Google BigQuery World Bank World Development Indicators SQL database. After scrubbing the Olympic nation names from the three datasets to match, I merged them together. This resulted in 203 unscrubbed datapoints across 10 features.

## Scrub Data

![Unscrubbed Olympic medal dataset df.info](https://github.com/blantj/olympic_medal_regression/blob/main/Images/df.info.png)

In order to scrub the Olympic medals dataset, I used a df.info() to see what scrubbing was necessary. I first replaced missing medal total values with 0 to reflect that these missing countries hadn't won any medals. I next coded all countries that hadn’t won Olympic medals to the 0 class and countries that had won medals to the 1 class for use in classification modeling.

![Olympic medal dataset Isolation Forest model](https://github.com/blantj/olympic_medal_regression/blob/main/Images/Isolation_Forest.png)

I dropped the country name variable not useful for modeling. I also dropped datapoints missing 4 or more features, as the imputed values for these nations would be unreliable. I subsequently imputed all remaining missing values. I finally built an Isolation Forest model, which confirmed that there were no outlier datapoints that needed to be removed. I replaced all feature values greater than 4 standard deviations from the mean with the 4 standard deviation value in order to mitigate the reduction in model performance that values far from their feature means can cause. After scrubbing, the dataset included 193 datapoints across 9 features.

## Explore Data

![Olympic medal dataset class distribution](https://github.com/blantj/olympic_medal_regression/blob/main/Images/Class_Distribution.png)

The Olympic dataset was slightly class imbalanced with 91 medal winning countries and 102 non-medal winning countries. I didn’t rebalance the dataset as it was already close enough to balanced to allow for effective modeling. Instead, I used f1 score as my principal evaluation metric in order to ensure that modeling would prioritize being equally effective at classifying both classes.

![Olympic medal dataset Variance Inflation Factor](https://github.com/blantj/olympic_medal_regression/blob/main/Images/VIF.png)

I wanted to make sure that there were no correlated features in the dataset that would make Logistic Regression coefficients less interpretable. I used Variance Inflation Factor to measure feature correlations in the Olympic dataset. GDP Per Capita had the highest VIF score at 3.3, meaning that no excess correlation existed.

## Model Data

![Olympic medal dataset modeling evaluation metrics overview](https://github.com/blantj/olympic_medal_regression/blob/main/Images/Modeling.png)

As part of the modeling process, I built a baseline Dummy Classifier, 5 individual models and a Voting Classifier. All models outperformed the baseline Dummy Classifier (f1 Score: .48, Accuracy: .45). The top performing individual model was SVM (f1 Score: .77, Accuracy: .78), while the top performing overall model was an SVM/XGBoost Voting Classifier (f1 Score: .80, Accuracy: .81).

![Olympic medal dataset Voting Classifier confusion matrix](https://github.com/blantj/olympic_medal_regression/blob/main/Images/Confusion_Matrix.png)

An analysis of the Voting Classifier confusion matrix confirmed the model’s strong performance. 54.4% of model predictions were for the no medals class, consistent with the slightly higher 52.9% weighting for this class. The model performed evenly across both classes with an 81% accuracy for no medal predictions and an 82% accuracy for medal predictions.

## Analyze Results

![Olympic medal dataset Logistic Regression coefficients](https://github.com/blantj/olympic_medal_regression/blob/main/Images/Logistic_Regression_Coefficients.png)

In order to get a better understanding of the drivers of model performance, I pulled Logistic Regression (f1 Score: .73, Accuracy: .71) feature coefficients. The two strongest positive predictors of winning Olympic medals were Population (Coefficient: .81) and Urban Population Proportion (Coefficient: .70). The two strongest negative predictors of Olympic medal wins were Ages 20–34 Population Proportion (Coefficient: -.43) and Migrant Population Proportion (Coefficient: -.33).

## Conclusion

I set out to build a model that could classify whether a nation won a medal at the 2020 Summer Olympics. This would allow the 113 country majority that didn’t win a medal at the 2020 Olympics to better understand their performance relative to model expectations as to whether they should have won a medal. The top performing SVM/XGBoost Voting Classifier model (f1 Score: .80, Accuracy: .81) strongly outperformed the baseline Dummy Classifier model (f1 Score: .48, Accuracy: .45).

To better understand the drivers of model performance, I pulled Logistic Regression (f1 Score: .73, Accuracy: .71) feature coefficients. The two largest coefficients positively influencing whether a country won a medal at the 2020 Olympics were Population (Coefficient: .81) and Urban Population Proportion (Coefficient: .70). The two largest negative coefficients were Ages 20–34 Population Proportion (Coefficient: -.43) and Migrant Population Proportion (Coefficient: -.33). Countries could also use model probability predictions to understand their predicted probability of winning a medal at the 2020 Summer Olympics. This would give a deeper understanding of how they performed at the Olympics relative to expectations.

# Github Files
[Obtaining_Data.ipynb](https://github.com/blantj/olympic_medal_regression/blob/main/Obtaining_Data.ipynb) :  2020 Olympics Medal Winning Countries Classification Obtaining Dataset

[Modeling.ipynb](https://github.com/blantj/olympic_medal_regression/blob/main/Modeling.ipynb) :  2020 Olympics Medal Winning Countries Classification Modeling

# Sources
Olympics: https://olympics.com/ioc/national-olympic-committees/

CBS Sports: https://www.cbssports.com/olympics/news/tokyo-olympics-final-medal-table-usa-leads-in-in-gold-silver-bronze-and-overall-with-more-than-100-medals/

Wolrd Bank: https://console.cloud.google.com/marketplace/product/the-world-bank/wdi

