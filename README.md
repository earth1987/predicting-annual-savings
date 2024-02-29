# Predictive & Econometric Modelling for Annual Savings

Annual net savings are forecasted at customer level using Ridge Regression & Random Forests. Additionally, ordinary least squares is utilised to develop an econometric model to explain the relationship between explanatory variables and savings.

## File structure

<pre>
|- predicting-annual-savings/
   |- custom_funcs.py
|- data/
   |- raw/
   |- cleaned/    
|- notebooks/
   |- data_exploration.ipynb
   |- econometrics.ipynb
   |- predictive-modelling.ipynb
   |- figures/
|- .gitignore
|- LICENSE
|- README.md
</pre>

## Econometrics

**Set-up:**

An ordinary least squares (OLS) model was constructed to predict savings above zero. Key steps are outlined below:

- Breakpoint dummy variables and corresponding interaction terms were incorporated for age, aiming to capture the observed non-linearity.
  
- Similarly, a breakpoint dummy variable and its corresponding interaction term were added for 'savings_bal_lbg' to account for a breakpoint.
  
- Feature selection was carried out by initially fitting the model with all features and subsequently eliminating those deemed insignificant, with a p-value below 0.05.
  
- To ensure the validity of interpretation, normality of residuals was confirmed prior to further analysis.

**Evaluation:**

The coefficients for OLS were interpreted as follows:

1. **const:** It suggests that when all predictor variables are zero, the expected annual net savings is approximately -5655.25.
     
2. **credit_score:** For each one-unit increase in credit score, the expected annual net savings is estimated to increase by 14.01 units.
   
3. **housing_spend:** For each one-unit increase in housing spending, the expected annual net savings is estimated to increase by 0.0143 units.
   
4. **age_dummy_2:** It suggests that, compared to the reference group (ages 1-24 and 55+), the expected annual net savings is lower on average by 3013.94 units for individuals between 25-54.
    
5. **age_dummy_3:** It suggests that, compared to the reference group (ages 1-54), the expected annual net savings is lower on average by 3385.37 units for individuals aged 55 years and above.

6. **age:**
   
    * The interaction terms must be considered when considering the coefficient for age.

    * For individuals less than 25 years in age, for each one-unit increase in age, the expected annual net savings is estimated to increase by 0.84 units (38.45-37.61) compared to the reference group (ages 25+).
  
    * For individuals between 25 years and 54 years of age, for each one-unit increase in age, the expected annual net savings is estimated to increase by 7.75 units (38.45-30.7) compared to the reference group (ages 1-24 and 55+).

    * For individuals above 55 years of age, for each one-unit increase in age, the expected annual net savings is estimated to increase by -6.35 units (38.45-44.8) compared to the reference group (ages <55).

7. **savings_bal_lbg:**

    * The interaction term must be considered when considering the coefficient for 'savings_bal_lbg'.
      
    * For each unit increase in savings balance, the expected annual net savings is estimated to increase by 0.04 units (0.0060-0.0056).
  
      Note: It remains unclear why this breakpoint occurs. Further investigations are necessary.
    
9. **zero_savings_bal:** It suggests that, compared to the reference group (individuals with savings at the beginnning of the year), the expected annual net savings is on average lower by 1425.66 units for individuals with no savings at the beginning of the year.
    
10. **ethnicity_group_South Asian:** It suggests that, compared to the reference group, the expected annual net savings is higher by 775.42 units for individuals in the South Asian ethnicity group.

    Note: Earlier analysis of pairwise associations suggested annual savings were highest for White British individuals.

## Predictive modelling

**Set-up:**

Due to time constraints, predictive models are only developed to predict annual savings above zero. Two models were created.
  
* A ridge regression model was implemented, incorporating an additional L2 regularisation term to counteract overfitting. Given that the ordinary least squares model fitted the data very well, the same features were used.

* A random forest model was tested as it can handle increased model complexity without requiring the additional interaction terms (which can lead to overfitting in regression models).

**Optimisation:**

Five-fold cross-validation was used to assess the stability of optimised models. Both models exhibit reasonable stability and high performance, characterised by elevated R-squared values and normal residuals. However, the ridge regression model performs better than the random forest across all metrics.

|Model|Metric|Mean +/- Standard Deviation|
|---|---|---|
|Ridge|R-squared|0.923 +/- 0.002|
|Ridge|MAE|1456.088 +/- 16.216|
|Ridge|MSE|1827.083 +/- 71606.961|
|Random Forest|R-squared|0.911 +/- 0.002|
|Random Forest|MAE|1551.046 +/- 11.678|
|Random Forest|MSE|1954.19 +/- 75186.545|

**Test Set Evaluation:**

The ridge regression model was retrained using the entire training set. Predictive performance was then assesed using the test set. The goodness of fit and predictive performance are very good. Furthermore, the residuals remain normally distributed suggesting the coefficients can be interpreted.

|Model|Metric|Value|
|---|---|---|
|Ridge|R-squared|0.92|
|Ridge|MAE|1471.7|
|Ridge|MSE|3396536.8|

**Conclusion:**

The final ridge regression model is advantageous from several perspectives. Firstly, it outperforms the random forest model on both R-squared and mean absolute error. Secondly, if transparency is crucial, ridge regression is more explainable than a random forest. While feature importance during the training process can be determined for random forests, the coefficients in ridge regression are readily available and more easily interpreted.* Next, as linear regression models are less complex, they may be less prone to capturing subtle biases in the data and therefore unintended discrimination. Finally, linear regression models are computationally less intensive than random forests.

Although the predictive performance of the model is high, it's important to note it's only applicable to individuals with annual savings above zero. Before utilising the model for decision-making, it might be necessary to develop a model to predict the probability of non-zero savings. Preliminary work in the econometrics workbook suggested that there might not be enough signal in the data for this latter task. Future work should involve testing non-linear models on this particular challenge and/or collecting more data.

\*Due to the additional regularisation term, the ridge regression model likely exhibits lower variance than the ordinary least squares model at the cost of increased bias in the coefficient estimates.