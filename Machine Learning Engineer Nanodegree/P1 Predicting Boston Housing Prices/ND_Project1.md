

```python
from __future__ import print_function
```


```python
import notebook
#notebook.nbextensions.check_nbextension("usability/python-markdown/", user = True)
E = notebook.nbextensions.EnableNBExtensionApp()
E.enable_nbextension("usability/python-markdown/main")
```


```python
from IPython.display import HTML

HTML('''<script>
code_show=true; 
function code_toggle() {
 if (code_show){
 $('div.input').hide();
 } else {
 $('div.input').show();
 }
 code_show = !code_show
} 
$( document ).ready(code_toggle);
</script>
<form action="javascript:code_toggle()"><input type="submit" value="Click here to toggle on/off the raw code."></form>''')
```




<script>
code_show=true; 
function code_toggle() {
 if (code_show){
 $('div.input').hide();
 } else {
 $('div.input').show();
 }
 code_show = !code_show
} 
$( document ).ready(code_toggle);
</script>
<form action="javascript:code_toggle()"><input type="submit" value="Click here to toggle on/off the raw code."></form>



#Machine Learning Engineer Nanodegree

##Project 1: Predicting Boston Housing Prices


```python
from sklearn import datasets

CLIENT_FEATURES = [[11.95, 0.00, 18.100, 0, 0.6590, 5.6090, 90.00, 1.385, 24, 680.0, 20.20, 332.09, 12.13]]

city_data = datasets.load_boston()
#print(city_data["DESCR"])

housing_prices = city_data.target
housing_features = city_data.data

feature_names = city_data.feature_names
feature_desc = ["Per Capita Crime Rate by Town",
                "Proportion of Residential Land Zoned for Lots over 25,000 sq.ft.",
                "Proportion of Non-retail Business Acres per Town",
                "Charles River Dummy Variable (= 1 if Tract Bounds River; 0 Otherwise)",
                "Nitric Oxides Concentration (Parts per 10 Million)",
                "Average Number of Rooms per Dwelling",
                "Proportion of Owner-occupied Units Built Prior to 1940",
                "Weighted Distances to Five Boston Employment Centres",
                "Index of Accessibility to Radial Highways",
                "Full-value Property-tax Rate per $10,000",
                "Pupil-teacher Ratio by Town",
                "1000(Bk - 0.63)^2 where Bk is the Proportion of Blacks by Town",
                "% Lower Status of the Population"]
```

###Introduction

This documents presents results for the first project within the Machine Learning Engineer Nanodegree program. This assessment required the student to leverage machine learning techniques in order to quantify a client's house price within the Boston Area.

###Data


```python
import pandas as pd
import numpy as np

total_houses = len(housing_features)
features_shape = np.shape(housing_features)
total_features = int(features_shape[1])

minimum_price = round(np.amin(housing_prices) * 1000, 2)
maximum_price = round(np.amax(housing_prices) * 1000, 2)
mean_price = round(np.mean(housing_prices) * 1000, 2)
median_price = round(np.median(housing_prices) * 1000, 2)
std_dev = round(np.std(housing_prices) * 1000, 2)

list_stats = [["Total houses", total_houses],
              ["Total features", total_features],
              ["Minimum price", minimum_price],
              ["Maximum price", maximum_price],
              ["Mean price", mean_price],
              ["Median price", median_price],
              ["Price standard deviation", std_dev]]

print("Table 1: Dataset Statistics Table")
df_results = pd.DataFrame(list_stats, columns = ["Statistic", "Value"])
df_results
```

    Table 1: Dataset Statistics Table
    




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Statistic</th>
      <th>Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Total houses</td>
      <td>506.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Total features</td>
      <td>13.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Minimum price</td>
      <td>5000.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Maximum price</td>
      <td>50000.00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Mean price</td>
      <td>22532.81</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Median price</td>
      <td>21200.00</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Price standard deviation</td>
      <td>9188.01</td>
    </tr>
  </tbody>
</table>
</div>



This assessment uses house price data from the StatLib library which is maintained at Carnegie Mellon University, and hosted on the <a href = "https://archive.ics.uci.edu/ml/datasets/Housing">UCI Machine Learning Repository</a>. There are {{total_houses}} houses and {{total_features}} features within the dataset. The minmum house price is {{minimum_price}}, maximum price is {{maximum_price}} and mean price is {{mean_price}}, all in dollars.

###Question 1

Of the features available for each data point, choose three that you feel are significant and give a brief description for each of what they measure.

####Answer

Scatterplots for each feature against the House Price variable are shown below. Each scatterplot also has a fitted OLS regression line overlay.


```python
import statsmodels.api as sm
import numpy as np
import matplotlib.gridspec as gridspec
import matplotlib.pyplot as plot
%matplotlib inline

fig = plot.figure(figsize = (14, 22))
gs = gridspec.GridSpec(7, 2)

for f in enumerate(feature_names):
    feat_desc = feature_desc[f[0]]
    
    if f[0] <= 6:
        ax = plot.subplot(gs[f[0], 0])
    else:
        ax = plot.subplot(gs[f[0]-7, 1])
    
    plot.tight_layout()
    plot.title("Figure " + str(f[0]) + ": House Prices vs.\n" + feat_desc)
    plot.plot(housing_features[:,f[0]], housing_prices, "bo", label = f[1])
    plot.legend()
    plot.xlabel(feat_desc)
    plot.ylabel("House Price ($1000's)")
    
    label = housing_prices
    feat = housing_features[:,f[0]]
    feat = sm.add_constant(feat)
    ols_model = sm.OLS(label, feat)
    ols_fitted = ols_model.fit()
    feat_pred = np.linspace(feat.min(), feat.max())
    feat_pred2 = sm.add_constant(feat_pred)
    label_pred = ols_fitted.predict(feat_pred2)
    
    plot.plot(feat_pred, label_pred)
    fig.add_subplot(ax)
    
plot.show()
```


![png](output_15_0.png)


There are no extreme outliers visible within any of the available features. However, the scatterplots do suggest that some features may have superior correlation to the House Price variable.

A summary table is shown below for each features fitted OLS regression statistics against House Prices. The table has been sorted according to descending $R^2$ measures.


```python
import statsmodels.api as sm
import pandas as pd

resultcols = ["feature",
              "description", 
              "coefficient", 
              "t-stat", 
              "p-value",
              "r^2"]

df_results = pd.DataFrame([])

for f in enumerate(feature_names):
    feat_desc = feature_desc[f[0]]
    label = housing_prices
    feat = housing_features[:,f[0]]
    feat = sm.add_constant(feat)
    ols_model = sm.OLS(label, feat)
    ols_fitted = ols_model.fit()
    
    coeff = ols_fitted.params[1] #coefficient
    t_stat = ols_fitted.tvalues[0] #t-stat
    p_value = ols_fitted.pvalues[0] #p-value
    r2 = ols_fitted.rsquared #R^2
    
    df_temp = pd.DataFrame([[f[1],
                             feat_desc, 
                             coeff, 
                             t_stat, 
                             p_value, 
                             r2]], 
                           index = [f[0]], columns = resultcols)

    df_results = df_results.append(df_temp)

print("Table 2: Feature Regression Statistics Table")
df_results.sort_values(by = "r^2", ascending = False)
```

    Table 2: Feature Regression Statistics Table
    




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>feature</th>
      <th>description</th>
      <th>coefficient</th>
      <th>t-stat</th>
      <th>p-value</th>
      <th>r^2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12</th>
      <td>LSTAT</td>
      <td>% Lower Status of the Population</td>
      <td>-0.950049</td>
      <td>61.415146</td>
      <td>3.743081e-236</td>
      <td>0.544146</td>
    </tr>
    <tr>
      <th>5</th>
      <td>RM</td>
      <td>Average Number of Rooms per Dwelling</td>
      <td>9.102109</td>
      <td>-13.084226</td>
      <td>6.950229e-34</td>
      <td>0.483525</td>
    </tr>
    <tr>
      <th>10</th>
      <td>PTRATIO</td>
      <td>Pupil-teacher Ratio by Town</td>
      <td>-2.157175</td>
      <td>20.581406</td>
      <td>9.077444e-69</td>
      <td>0.257847</td>
    </tr>
    <tr>
      <th>2</th>
      <td>INDUS</td>
      <td>Proportion of Non-retail Business Acres per Town</td>
      <td>-0.648490</td>
      <td>43.536622</td>
      <td>6.704987e-173</td>
      <td>0.233990</td>
    </tr>
    <tr>
      <th>9</th>
      <td>TAX</td>
      <td>Full-value Property-tax Rate per $10,000</td>
      <td>-0.025568</td>
      <td>34.768331</td>
      <td>5.519383e-136</td>
      <td>0.219526</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NOX</td>
      <td>Nitric Oxides Concentration (Parts per 10 Mill...</td>
      <td>-33.916055</td>
      <td>22.827995</td>
      <td>9.866245e-80</td>
      <td>0.182603</td>
    </tr>
    <tr>
      <th>0</th>
      <td>CRIM</td>
      <td>Per Capita Crime Rate by Town</td>
      <td>-0.412775</td>
      <td>58.676212</td>
      <td>2.168010e-227</td>
      <td>0.148866</td>
    </tr>
    <tr>
      <th>8</th>
      <td>RAD</td>
      <td>Index of Accessibility to Radial Highways</td>
      <td>-0.403095</td>
      <td>46.963616</td>
      <td>3.282092e-186</td>
      <td>0.145639</td>
    </tr>
    <tr>
      <th>6</th>
      <td>AGE</td>
      <td>Proportion of Owner-occupied Units Built Prior...</td>
      <td>-0.123163</td>
      <td>31.006388</td>
      <td>6.814198e-119</td>
      <td>0.142095</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ZN</td>
      <td>Proportion of Residential Land Zoned for Lots ...</td>
      <td>0.142140</td>
      <td>49.247899</td>
      <td>9.489803e-195</td>
      <td>0.129921</td>
    </tr>
    <tr>
      <th>11</th>
      <td>B</td>
      <td>1000(Bk - 0.63)^2 where Bk is the Proportion o...</td>
      <td>0.033593</td>
      <td>6.774503</td>
      <td>3.491585e-11</td>
      <td>0.111196</td>
    </tr>
    <tr>
      <th>7</th>
      <td>DIS</td>
      <td>Weighted Distances to Five Boston Employment C...</td>
      <td>1.091613</td>
      <td>22.498584</td>
      <td>4.008955e-78</td>
      <td>0.062464</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CHAS</td>
      <td>Charles River Dummy Variable (= 1 if Tract Bou...</td>
      <td>6.346157</td>
      <td>52.902318</td>
      <td>7.002789e-208</td>
      <td>0.030716</td>
    </tr>
  </tbody>
</table>
</div>



Subjectively, the coefficient polarity for each of the regressed features shown above seems reasonable. The regression fit does vary significantly for each however, with Feature 12: % Lower Status of the Population having the highest $R^2$ value, and Feature 3: Charles River Dummy Variable having the lowest $R^2$ value.

Based on an initial assessment of each feature, I would nominate a model which included Feature 5: Average Number of Rooms per Dwelling, Feature 10: Pupil-teacher Ratio by Town, and finally, Feature 12: % Lower Status of the Population.

###Question 2

Using your client's feature set `CLIENT_FEATURES`, which values correspond with the features you've chosen above?

####Answer


```python
import pandas as pd

pd.set_option("display.max_colwidth", 100)
```


```python
import pandas as pd

resultcols = ["feature",
              "description",
              "minValue",
              "maxValue",
              "clientValue",
              "prelimSelection"]

df_results = pd.DataFrame([])

i = 0
for f in feature_names:
    if i == 5 or i == 10 or i == 12:
        pselec = 1
    else:
        pselec = 0
    
    df_temp = pd.DataFrame([[f,
                             feature_desc[i],
                             min(housing_features[:,i]),
                             max(housing_features[:,i]),
                             CLIENT_FEATURES[0][i],
                             pselec]],
                             index = [i], columns = resultcols)

    df_results = df_results.append(df_temp)
    i += 1
    
print("Table 3: Client Feature Value Comparison Table")
df_results
```

    Table 3: Client Feature Value Comparison Table
    




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>feature</th>
      <th>description</th>
      <th>minValue</th>
      <th>maxValue</th>
      <th>clientValue</th>
      <th>prelimSelection</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CRIM</td>
      <td>Per Capita Crime Rate by Town</td>
      <td>0.00632</td>
      <td>88.9762</td>
      <td>11.950</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ZN</td>
      <td>Proportion of Residential Land Zoned for Lots over 25,000 sq.ft.</td>
      <td>0.00000</td>
      <td>100.0000</td>
      <td>0.000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>INDUS</td>
      <td>Proportion of Non-retail Business Acres per Town</td>
      <td>0.46000</td>
      <td>27.7400</td>
      <td>18.100</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CHAS</td>
      <td>Charles River Dummy Variable (= 1 if Tract Bounds River; 0 Otherwise)</td>
      <td>0.00000</td>
      <td>1.0000</td>
      <td>0.000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NOX</td>
      <td>Nitric Oxides Concentration (Parts per 10 Million)</td>
      <td>0.38500</td>
      <td>0.8710</td>
      <td>0.659</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>RM</td>
      <td>Average Number of Rooms per Dwelling</td>
      <td>3.56100</td>
      <td>8.7800</td>
      <td>5.609</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>AGE</td>
      <td>Proportion of Owner-occupied Units Built Prior to 1940</td>
      <td>2.90000</td>
      <td>100.0000</td>
      <td>90.000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>DIS</td>
      <td>Weighted Distances to Five Boston Employment Centres</td>
      <td>1.12960</td>
      <td>12.1265</td>
      <td>1.385</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>RAD</td>
      <td>Index of Accessibility to Radial Highways</td>
      <td>1.00000</td>
      <td>24.0000</td>
      <td>24.000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>TAX</td>
      <td>Full-value Property-tax Rate per $10,000</td>
      <td>187.00000</td>
      <td>711.0000</td>
      <td>680.000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>PTRATIO</td>
      <td>Pupil-teacher Ratio by Town</td>
      <td>12.60000</td>
      <td>22.0000</td>
      <td>20.200</td>
      <td>1</td>
    </tr>
    <tr>
      <th>11</th>
      <td>B</td>
      <td>1000(Bk - 0.63)^2 where Bk is the Proportion of Blacks by Town</td>
      <td>0.32000</td>
      <td>396.9000</td>
      <td>332.090</td>
      <td>0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>LSTAT</td>
      <td>% Lower Status of the Population</td>
      <td>1.73000</td>
      <td>37.9700</td>
      <td>12.130</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
import pandas as pd

pd.reset_option("display.max_colwidth")
```

When referring back to the OLS regression statistics for each feature, we see that a <b>greater</b> amount of Average Number of Rooms per Dwelling, or a <b>lower</b> amount of Pupil-teacher Ratio by Town, or a <b>lower</b> amount of % Lower Status of the Population all lead to a <b>greater</b> House Price.

Based on the client's features, we can see that he or she has a Pupil-teacher Ratio by Town which is close to the maximum value within the dataset. This would suggest a lower price for the clients house. However, the clients Average Number of Rooms per Dwelling and % Lower Status of the Population lie approximately within the median of the range of the dataset.

###Question 3

Why do we split the data into training and testing subsets for our model?

####Answer


```python
from sklearn import cross_validation

def shuffle_split_data(X, y):
    """ Shuffles and splits data into 70% training and 30% testing subsets,
        then returns the training and testing subsets. """
    
    X_train, X_test, y_train, y_test = cross_validation.train_test_split(X, y, test_size=0.30)
    return X_train, y_train, X_test, y_test

X_train, y_train, X_test, y_test = shuffle_split_data(housing_features, housing_prices)

training_subset = len(y_train)
testing_subset = len(y_test)
```

According to <a href = "http://scikit-learn.org/stable/modules/cross_validation.html">scikit learn</a>, learning the parameters of a prediction function and testing it on the same data is a methodological mistake: a model that would just repeat the labels of the samples that it has just seen would have a perfect score but would fail to predict anything useful on yet-unseen data. This situation is called overfitting.

In order to avoid overfitting, analysts commonly employ techniques to split the data into training and testing subsets prior to assessing model performance. There are a number of available methods to perform such a function. A simple train-test split for example, may bear some advantage for applications which involve large scale datasets and/or where computation time is a critical factor. 

The resulting number of observations within the testing and training subsets for a 30% split are {{testing_subset}} and {{training_subset}} respectively.

###Question 4

Which performance metric below did you find was most appropriate for predicting housing prices and analyzing the total error. Why?*
- *Accuracy*
- *Precision*
- *Recall*
- *F1 Score*
- *Mean Squared Error (MSE)*
- *Mean Absolute Error (MAE)*

####Answer

The choice of performance metric depends on the characteristics of the underlying data, type of model employed, and focus of application. More generally, Mean Squared Error (MSE) is a good performance metric for applications where the model follows a normal distribution, however, the sensitivity of the MSE to outliers is one of the most common concerns when using this metric (<a href = "https://www.researchgate.net/publication/272024186_Root_mean_square_error_RMSE_or_mean_absolute_error_MAE-_Arguments_against_avoiding_RMSE_in_the_literature">ref</a>). In light of this, MSE has been selected as the preferred metric for this assessment, since there are little to no identified outliers and the selected model assumes errors follow a normal distribution.


```python
from sklearn.metrics import mean_squared_error

def performance_metric(y_true, y_predict):
    """ Calculates and returns the total error between true and predicted values
        based on a performance metric chosen by the student. """
    
    error = mean_squared_error(y_true, y_predict)
    return error

total_error = performance_metric(y_train, y_train)
```

###Question 5

What is the grid search algorithm and when is it applicable?

####Answer


```python
from sklearn.tree import DecisionTreeRegressor
from sklearn.metrics import make_scorer
from sklearn.metrics import mean_squared_error
from sklearn.grid_search import GridSearchCV
from sklearn import datasets

def fit_model(X, y):
    """ Tunes a decision tree regressor model using GridSearchCV on the input data X 
        and target labels y and returns this optimal model. """

    regressor = DecisionTreeRegressor()
    parameters = {'max_depth':(1,2,3,4,5,6,7,8,9,10)}
    scoring_function = make_scorer(mean_squared_error, greater_is_better = False)
    reg = GridSearchCV(regressor, param_grid = parameters,scoring = scoring_function)
    reg.fit(X, y)

    return reg

city_data = datasets.load_boston()
housing_prices = city_data.target
housing_features = city_data.data
reg = fit_model(housing_features, housing_prices)

param_range = [1,2,3,4,5,6,7,8,9,10]
min_param = min(param_range)
max_param = max(param_range)
best_param = reg.best_params_["max_depth"]
```

According to <a href = "http://scikit-learn.org/stable/modules/grid_search.html">scikit learn</a>, search algorithms allow model parameters to be set by searching a parameter space for the best evaluating estimator performance score. Under such approach, the analyst is able to avoid selecting a single, and potentially non-optimized set of parameters for their desired model, and instead is provided with a set of parameters which are optimized according to their desired performance metric.

There are a range of search algorithms available to the analyst for parameter selection. For this assessment, the <a href = "http://scikit-learn.org/stable/modules/cross_validation.html">GridSearchCV procedure</a> has been implemented via the scikit learn package, which implements a fit and score method, over the cross-validated datasets. In this case, the range of parameter passed to the GridSearchCV proceedure are varying depth levels for the DecisionTreeRegressor algorithm. The depth levels range from {{min_param}} to {{max_param}}.

###Question 6

What is cross-validation, and how is it performed on a model? Why would cross-validation be helpful when using grid search?

####Answer

Cross-validation is provides a number of advantages over a simple 'split' method, namely:

- Reduces overfitting: If GridsearchCV were limited to a single training subset which is inconsistent with the wider dataset (e.g. contains some form of data anomoly), the resulting model may be overfit to the inconsistent subset of data. Through a process of random splitting, cross-validation optimizes parameters over the entire dataset, limiting the representation of inconsistent subsets of data.
- Maximizes data usage: For datasets which are limited in size, cross-validation allows for an extensive exploitation of available data allowing assessing the real potential of our algorithm in terms of performance metrics.


For this assessment, a <a href = "http://scikit-learn.org/stable/modules/cross_validation.html">k-fold cross-validation procedure</a> has been implemented via the scikit learn package. Under this technique, the training set is split into k smaller sets, with the model trained using k-1 of the folds and tested on the remaining part of the data. This folding process continues in order to capture insights from the entire dataset. Under each fold, the GridSearchCV proceedure also optimizes the parameters set.

Below, a set of evaluations for the DecisionTreeRegressor algorithm according to our chosen performance metric are show. Each evaluation carries an increasing 'max_depth' parameter on the full training set, and can be used to observe how model complexity affects learning and testing errors.


```python
def learning_curves(X_train, y_train, X_test, y_test):
    """ Calculates the performance of several models with varying sizes of training data.
        The learning and testing error rates for each model are then plotted. """
    
    fig = plot.figure(figsize=(10,8))

    sizes = np.round(np.linspace(1, len(X_train), 50))
    train_err = np.zeros(len(sizes))
    test_err = np.zeros(len(sizes))
    
    for k, depth in enumerate([1,3,6,10]):
        for i, s in enumerate(sizes):
            regressor = DecisionTreeRegressor(max_depth = depth)
            regressor.fit(X_train[:s], y_train[:s])
            train_err[i] = performance_metric(y_train[:s], regressor.predict(X_train[:s]))
            test_err[i] = performance_metric(y_test, regressor.predict(X_test))
        
        ax = fig.add_subplot(2, 2, k+1)
        ax.plot(sizes, test_err, lw = 2, label = 'Testing Error')
        ax.plot(sizes, train_err, lw = 2, label = 'Training Error')
        ax.legend()
        ax.set_title('max_depth = %s'%(depth))
        ax.set_xlabel('Number of Data Points in Training Set')
        ax.set_ylabel('Total Error')
        ax.set_xlim([0, len(X_train)])
        
    fig.suptitle('Decision Tree Regressor Learning Performances', fontsize = 18, y = 1.03)
    fig.tight_layout()
    fig.show()
```


```python
def model_complexity(X_train, y_train, X_test, y_test):
    """ Calculates the performance of the model as model complexity increases.
        The learning and testing errors rates are then plotted. """
    
    max_depth = np.arange(1, 14)
    train_err = np.zeros(len(max_depth))
    test_err = np.zeros(len(max_depth))

    for i, d in enumerate(max_depth):
        regressor = DecisionTreeRegressor(max_depth = d)
        regressor.fit(X_train, y_train)
        train_err[i] = performance_metric(y_train, regressor.predict(X_train))
        test_err[i] = performance_metric(y_test, regressor.predict(X_test))

    plot.figure(figsize=(7, 5))
    plot.title('Decision Tree Regressor Complexity Performance')
    plot.plot(max_depth, test_err, lw=2, label = 'Testing Error')
    plot.plot(max_depth, train_err, lw=2, label = 'Training Error')
    plot.legend()
    plot.xlabel('Maximum Depth')
    plot.ylabel('Total Error')
    plot.show()
```


```python
from sklearn.tree import DecisionTreeRegressor
import numpy as np
import matplotlib.pyplot as plot
%matplotlib inline

learning_curves(X_train, y_train, X_test, y_test)
```

    C:\Anaconda3\envs\python3\lib\site-packages\ipykernel\__main__.py:14: DeprecationWarning: using a non-integer number instead of an integer will result in an error in the future
    C:\Anaconda3\envs\python3\lib\site-packages\ipykernel\__main__.py:15: DeprecationWarning: using a non-integer number instead of an integer will result in an error in the future
    C:\Anaconda3\envs\python3\lib\site-packages\matplotlib\figure.py:397: UserWarning: matplotlib is currently using a non-GUI backend, so cannot show the figure
      "matplotlib is currently using a non-GUI backend, "
    


![png](output_49_1.png)


###Question 7

Choose one of the learning curve graphs that are created above. What is the max depth for the chosen model? As the size of the training set increases, what happens to the training error? What happens to the testing error?

####Answer

The four plots shown above vary the `max_depth` parameter of the model between 1, 3, 6 and 10. For all plots the testing error is greater than the training error, which is intuitive. In all cases, a very small amount of available datapoints in the training set leads to a large test error. As the number of datapoints increase, testing error tends to fall, however there are some notable spikes in both testing error over the range. At the same time, as the number of datapoints in the increases, training error tends to increase, showing that the model training routine is encountering a growing amount of generalized data.

###Question 8

Look at the learning curve graphs for the model with a max_depth of 1 and a max_depth of 10. When the model is using the full training set, does it suffer from high bias or high variance when the max_depth is 1? What about when the max_depth is 10?

####Answer

With a max_depth parameter of one, the model suffers from high bias, that is, the model performs relatively poorly on both the test and training sets. On the other hand, with a max_depth parameter of 10, the model suffers from overfitting. In this case, the training error is very minimal, which suggest that the model would struggle to generalize to unseen data.

###Question 9

From the model complexity graph, describe the training and testing errors as the max_depth increases. Based on your interpretation of the graph, which max_depth results in a model that best generalizes the dataset? Why?

####Answer


```python
model_complexity(X_train, y_train, X_test, y_test)
```


![png](output_61_0.png)


As model complexity increases, both the training and test error improves. It is important to note that both error measures see diminishing returns from each incremental increase in model complexity, with testing error seeing a greater diminishing return effect. In fact, as model complexity increases over the range, the testing error tends to flatten out beyond a maximum depth of four. It is from this point that the model is beginning to overfit the data.

###Question 10

Using grid search on the entire dataset, what is the optimal max_depth parameter for your model? How does this result compare to your intial intuition?

####Answer


```python
print("GridsearchCV optimial max_depth:", best_param)
```

    GridsearchCV optimial max_depth: 5
    

According to the results of the GridSearchCV algorithm, the optimal max_depth parameter for the selected model is {{best_param}}. Not surprisingly, this value is not too dissimilar from the suggested max_depth from the previous question. Selecting a max_depth value significantly greater or less than this value would result in performance issues as described above.

###Question 11

With your parameter-tuned model, what is the best selling price for your client's home? How does this selling price compare to the basic statistics you calculated on the dataset?

####Answer


```python
sale_price = round(reg.predict(CLIENT_FEATURES)[0] * 1000, 2)
print("Predicted sale price:", sale_price)
print("Dataset mean sale price", mean_price)
```

    Predicted sale price: 20967.76
    Dataset mean sale price 22532.81
    

According to the parameter-tuned model, the suggested selling price for the client's home is {{sale_price}} dollars, which is not too dissimilar from the mean price of {{mean_price}} dollars.

To further assess the predicted client sale price, we can use SKlearn to make a comparison of the predication against the Nearest Neighbours of the original house feature vector.


```python
from sklearn.neighbors import NearestNeighbors

def find_nearest_neighbor_indexes(x, X):  # x is your vector and X is the data set.
   neigh = NearestNeighbors( n_neighbors = 10 )
   neigh.fit( X)
   distance, indexes = neigh.kneighbors( x )
   return indexes

indexes = find_nearest_neighbor_indexes(CLIENT_FEATURES, housing_features)
sum_prices = []
for i in indexes:
    sum_prices.append(city_data.target[i])
neighbor_avg = round(np.mean(sum_prices) * 1000, 2)

print("Predicted sale price:", sale_price)
print("Nearest Neighbors average:", neighbor_avg)
```

    Predicted sale price: 20967.76
    Nearest Neighbors average: 21520.0
    

Here, the predicted sales price for the client's home lies closer to the average Nearest Neighbours price.

###Question 12

In a few sentences, discuss whether you would use this model or not to predict the selling price of future clients' homes in the Greater Boston area.

####Answer

I would be cautious in using such a model as the only means of infroming the client of an appropriate sales price. There are likely to be limitations due mainly to the leveraged dataset. For example, the dataset may be missing features which, if included within the model specification would add explanatory power over house prices. Not including these features would lead to a misspecified model which does not reflect the true dynamics of the house price market. Additionally, the age of the data may make it unreasonable to use, as over time, structural changes within the house price market may cause historical empirical relationships to become void.
