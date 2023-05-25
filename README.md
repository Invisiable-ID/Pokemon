# Pokemon
## Title and Background
The dataset is a sample of the data of the Pokemon, covering information on a total of 801 Pokemon elves from the first to the seventh generation. Data features include basic abilities, restraint abilities for other attributes, height, weight, type and so on. Data sources are https://serebii.net/
## Project Summary
The purpose of this experiment is to analyze factors that could influence Pokemon's comprehensive attributes. By analyzing different attributes, modeling them and training, we could find out the factors that may affect their BASE_TOTAL attribute. And for ordinary players, this project could give some advice about how to choose their own Pokemon.
## Result
### Data Processing and Analysis
#### Data Description
![Data Description](https://github.com/Invisiable-ID/Pokemon/assets/70438246/719b0d58-6c05-4e97-9845-96b277e7cfb0)

#### Missing Values of Attributes in the Table
![Missing Values](https://github.com/Invisiable-ID/Pokemon/assets/70438246/3605fad9-004a-4745-b17b-9021ff465253)

### Data Processing
By a log transformation on the BASE_TOTAL, data of BASE_TOTAL could be closer to Normal Distribution.
Histogram and Probability Plot before log transformation:
![hist(x=base_total,y=Frequency)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/af638929-a68e-4d94-8294-157e8b051d2d)
![image](https://github.com/Invisiable-ID/Pokemon/assets/70438246/6b75048d-3aa3-482a-a2db-09e7b8bd66cb)

Histogram and Probability Plot after log transformation:
![image (1)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/a036ecf5-51b8-467f-8214-952a67a9a2f3)
![image (2)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/0ba6bdf5-8705-4478-9f9f-e832f9899948)

### Correlation analysis
FIRST, select 10 features that may affect the BASE_TOTAL for analysis, and then fill in the missing values. 
NEXT, remove some of the attributes not related to BASE_TOTAL (column), such as NAME, Japanese_name, etc.
#### Data Visualization
Draw histogram: iterate through the feature column, and observe the influence of each factor on the BASE_TOTAL.
![image (3)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/315f0e5d-63f9-43d2-a5f0-ec7a533e481c)

Draw a BoxPlot: Loop through the feature column and generate subplots with BASE_TOTAL. Draw a BoxPlot to show the correlation between BASE_TOTAL and each feature.
![BoxPlot](https://github.com/Invisiable-ID/Pokemon/assets/70438246/20eaa7d0-2357-4534-86e4-f8fc3f2ba615)

Draw HeatMap: A higher value in the Matrix indicates a stronger correlation.
![HeatMap](https://github.com/Invisiable-ID/Pokemon/assets/70438246/e6bd68ee-442e-4ebb-a7bc-0c4ce9a6f410)

#### Univariate Analysis
Fit a Linear Regression Model: Correlation between HEIGHT and BASE_TOTAL
![image (6)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/a2f50ff8-5ba6-4a38-8afb-96c418a11502)

The same method fits the correlation between BASE_TOTAL and other main attributes(WEIGHT, ATTACK, SP_ATTACK, DEFENSE, SP_DEFENSE, SPEED).
![image (7)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/040f8ee2-c76e-48d0-b160-1f325b2e2732)
![image (8)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/2ca5761a-1700-45ca-b864-ef03a58599e3)

### Building Machine Learning Models
#### Regression Models
Feature dimension reduction: using PCA(Principal Component Analysis). Reduce the features down to 10 dimensions by PCA(n_components = 9).
Build several machine learning models, including Ordinary Least Squares, Ridge, Lasso, ElasticNet and Kernel Ridge. 
Cross validation and evaluation: The RMSLE_CV function is defined for cross validation and RMSLE(Root Mean Squared Logarithmic Error) of the model is calculated. The smaller the mean and standard deviation of the model output error, the more accurate the prediction result will be.

Output model evaluation results: mean sore(standard deviation)
* LinearRregression score: 0.0000 (0.0000)
* Ridge score: 0.2040 (0.0318)
* Lasso score: 0.0065 (0.0027)
* ElasticNet score: 0.0084 (0.0013)
* Kernel Ridge score: 1.9577 (0.7347)

It can be seen that the traditional linear regression model has the problem of overfitting, so the regularization term is added to reduce the impact of it.

##### What's regularization?
Regularization limits the complexity of the model by introducing additional penalty terms in the loss function. In linear models, ridge regression or lasso regression(L1 regularization) can be used to increase the regularization term.
Ridge regression limits the weight of the model by adding an L2 norm penalty to the loss function.
Lasso regression pushes the weight of the model to zero by adding L1 norm penalty to the loss function.
Using the ridge and lasso classes of the Scikit-learn library to apply regularization shows that the adjusted model improves.

#### Integrated learning
To further optimize the traditional regression model, the following build an ensemble learning model using the following ensemble learning methods:
Random Forest, Boosting, Gradient boosting, XGBoost and LGBMRegressorGBM.

The output error result is:
* RandomForest score: 29.8594 (4.9671)
* GBoost score: 18.2543 (3.7770)
* Xgboost score: 13.0888 (2.5929)
* LGBMRegressorGBM score: 15.6519 (1.9497)

#### Create a Fusion Model:
Define the loss function RMSLE: in order to compare different models, calculate its error with the real value as a criterion for the accuracy of the model prediction.

Meta Model is defined:
* _ _ init _ _: Initialization method that takes the base model list, metamodel, and cross-validation fold as parameters.
* Fit: A training method that trains the basic model and the meta-model, generates the prediction results of the basic model as meta-features, and then uses the meta-features to train the meta-model.
* Prediction: A prediction method that makes predictions on test data, first using the basic model to predict the predictions of the basic model, and then using these predictions as meta-features to make final predictions using the trained meta-model.

This creates an instance of stackingAveragedModels stacked _ averaged _ models, passing in three base models (ridge, lasso, and ENet) and a metamodel (liner) as parameters; The performance metric of stacked _ averaged _ models, namely root mean square logarithmic error (RMSLE), was calculated using cross-validation, and the evaluation results were output.

The final assessment results are:
* Stacking Averaged models score: 0.0039 (0.0008)

It can be seen that the fusion model is better.

* The error between the predicted value and the true value:
* stacked_averaged_models predicted data used for training: 0.0021819010658953543
* Other model predictions:
* model_xgb predicted data used for training: 1.1167302404241017
* GBoost predicted data used for training: 4.860769740247562

Plug in the test set for prediction and comparison, and adjust the integration parameters.

Prediction error:

* Predictive Training Dataset (train):0.06460921006668209
* Predictive test dataset (test):0.28745139204817566
* Forecast overall data (poke):0.1544654552818777

#### The last step is to export the forecast results in csv format.
### Model Training Results
It can be seen that the prediction error of the trained model to the final result is small, the accuracy is high, and it can accurately reflect the comprehensive attribute. The higher the proportion of stacked _ averaged _ models is, the smaller the prediction error will be.
## Pokemon Section Advice
### Count according to the classification characteristics of the Pokemon
According to the official introduction of Pokémon game, different generations of elves have different attributes and abilities. In order to more intuitively show the difference in the number of sprites of different generations, the histogram is drawn using the Python visualization function.
![image (9)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/ba90a9c6-c430-4237-9a43-724e445a2fa8)

As can be seen from the bar chart, the number of sprites is most concentrated in generation 5 and least in generation 6.
Next, count the distribution of the Pokemon under the different main attributes and draw a bar chart.
![image (10)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/b64ce02a-9d49-45a6-8468-af4f84e43e17)

The distribution of the number of Pokemon is shown in the figure, we can see that the distribution is mainly concentrated in the water system.
### Comprehensive Ability Distribution
Use Python visualization to see the distribution of comprehensive capabilities according to different attributes.(Violin Diagram)
![image (11)](https://github.com/Invisiable-ID/Pokemon/assets/70438246/54b35d8e-fb69-49fa-846b-8c2eb7d83b51)

### Excellent Pokemon Selection Advice
"BASE_TOTAL = 570" is a dividing line, and above this threshold is a good Pokemon. Considering the high rarity of the god Pokemon, the non-god category is mainly considered here, namely "Special Pokemon" (is _ legendary = 0).

The result is (name in Japanese):

* 2      Fushigibanaフシギバナ
* 5         Lizardonリザードン
* 8            Kamexカメックス
* 17          Pigeotピジョット
* 64          Foodinフーディン
* 79          Yadoranヤドラン
* 93           Gangarゲンガー
* 114          Garuraガルーラ
* 126         Kailiosカイロス
* 129       Gyaradosギャラドス

It is concluded that ordinary players should try to choose these ten Pokemons.
