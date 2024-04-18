# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) Project 2: Ames Housing Data & Kaggle Challenge

### Contents:
- [Problem Statement](#Problem-Statement)
- [Analysis](#Analysis)
- [Significant Plots](#Significant-Plots)
- [Conclusion](#Conclusion)

### Problem Statement

I have been contracted by Ames Planning and Zoning Commission to generate a model to assist in predicting home prices.  The ability to predict prices of homes can help the commission make planning decisions that promote housing affordability and cost of living.

---

### Dataset Dictionary

|Feature|Type|Source|Description|
|---|---|---|---|
|**train**|*various*|Kaggle|Training data used for modeling. Contains dozens of feature details about homes that could be used for modeling, inclduing the sale price.| 
|**validate**|*various*|Kaggle|Test data that will be ran against a model to predict home prices.  
|**train_clean**|*various*|Generated|Data set that has been modified and cleaned from the 'train' set.
|**validate_clean**|*float*|Generated|Data set that has been modified and cleaned from the 'validate' set.
|**khalbig_kaggle_submission**|*float*|Generated|My Kaggle submissions of home prices from the 'validate_clean' set.
|**sample_sub_reg**|*float*|Generated|Sample Kaggle submission using sale price mean.

#### Additional Research & Data
The following are a list of articles that were helpful in developing this project. 


1. [The Role of Urban Planning in Real Estate Development](https://blog.kurby.ai/the-role-of-urban-planning-in-real-estate-development/) - Article explaining how urban planning affects real estate development
2. [Harnessing Predictive Analytics for Urban Development](https://atcresearch.co/guest-blog/harnessing-predictive-analytics-for-urban-development/) - Article that explores different ways urban planners can use predictive analytics.
3. [Data Science and Cities: A Critical Approach](https://hdsr.mitpress.mit.edu/pub/1um18ajd/release/2) - Explores how the field of urban data science should be more than just collecting and modeling data.
---

### Analysis

NON-INTERPRETABLE MODEL 

TOTAL FEATURES USED
* 'neighborhood',
* 'overall_cond',
* 'bldg_type',
* 'kitchen_qual',
* 'central_air',
* 'gr_liv_area',
* 'garage_area',
* 'total_bsmt_sf',
* '1st_flr_sf',
* 'kitchen_qual * overall_qual * exter_qual',
* 'bedroom_abvgr',
* 'overall_qual',
* 'exter_qual',
* 'year_built'

The 'neighborhood' column was the first feature I wanted to add to my model.  My experience of living in Los Angeles informed me that home prices are influenced by what area of the city they are located.  I one hot encoded the feature and generated a model.  The R2 values for the test and train data were 0.57 and 0.59 respecitively.  Cross validation values looked similar.  I predicted house prices for the validation data and submitted this to the Kaggle competition, knowing there is room for improvelemt.

After further investigation into the various features I could use in the model, I chose the following:
* Neighborhood (already added to first model)
* Overall condition of home
* Building Type
* Kitchen Quality
* Central Air

The neighborhood feature was already one hot encoded, but I did the same for building type.  Kitchen quality had values of 'Fair', 'Typical', 'Good', and 'Excellent' so I changed those values to 0, 1, 2, and 3 respectively.  I also make a similar change to Central Air and changed 'yes' values to 1 and 'no' values to 0.  Overall condition is a numeric scale from 1 to 10 being the highest, so I left those values as-is.

R2 scores for this report 0.69 for training data and 0.71 for test data.  Cross validation R2 values were somewhat more varied, but all above my first attempt at a model.  Afterwards I tried to create an interaction term with overall quality and kitchen condition, but there was virtually no change in R2 value.

I pulled all of the quantitative columns and correlated them to sales price.  Features with stronger correlations to sales price were added to the model:
* Living Room Area
* Garage Area
* Total Basement Area
* First Floor Area

After cleaning and checking the linear relationship by plots, I created another model.  This one had a test R2 score of 0.82 and a train score of 0.86.  Cross validation looked similar.  I then tried to making interactive features of all four of these features, but the score went down to about 0.7.  Making interactive features of just the living room and first floor area made no change.

I also added in the 'year_built' feature.  This marginally increased the model score.

I added the number of bedrooms to my feature set.  I also changed all of the homes reporting zero bedrooms and changed them to the most frequently recorded number of bedrooms with SimpleImputer.  This also did not significantly change the model score.

Next I addded in overall quality to the dataset without making any transformations since it's numeric ordinal data.  This made a slight increase in the model score to a R2 of 0.83 and 0.89 for test data. Cross validation was in the high 80% range.  I made an interaction term with kitchen quality and exterior quality and this pushed my R2 scores up to 0.85 for the training data and 0.91 for test data.  Cross validation looked similar.  The RSME of the training data is 30877 and test data is 22953.

I added in a scaler for number of bedrooms and all area related features within the model with no significant change.

I attempted regularization with both Ridge and Lasso. The best alpha value for a Ridge regression was about 0.68 and for Laso it was 8.1.  Neither seemed to make a significant change in the R2 values.

INTERPRETABLE MODEL 

TOTAL FEATURES USED
* 'neighborhood',
* 'gr_liv_area',
* 'overall_qual',

This is a simplied version of my first, non-interpretable model.  No scalers were used and the neighborhood features was one hot encoded.  R2 values were not as high as my original model - train R2 was at 0.786 and test R2 value at 0.84.  The RMSE of the training data is about: 37093 and test data at 30492.  This seems on the higher side for home prices.  

LINE assumptions were mostly acceptable.  I believe it's safe to assume these homes were sold on different dates, which breaks the assumption of independence.  However, there is a linear relationship between features and home prices, as well as a general normality of residuals and randomness between residuals and home price predictions.

---

### Significant Plots
#### Designed to show relationships between feature and sale price.
* [Neighborhood](Plot_Images/stripplot_nhood_vs_price.png) 
* [Overall Quality](Plot_Images/stripplot_overall_qual_vs_price.png)
* [Living Room Area](Plot_Images/scatter_livingroomsqft_vs_price.png)
* [Kitchen Quality](Plot_Images/barplot_kitchen_qual_vs_price.png)
  
### Conclusion
---
I successfully created two models that output better predictions than the baseline.  One used various features, interaction terms, and one hot encoding.  The other model was scaled down from this, using only three features and is interpretable.  While I think this is a successful model considering the data provided, I believe it wouldn't be a complete model after having read the 'Data Science and Cities: A Critical Approach' article.  I do not believe a model like this should be used to inform policy decisions for a city until a more comprehensive approach is designed.  This includes consultation with people from a social sciences background and further research into how the data was collected.

