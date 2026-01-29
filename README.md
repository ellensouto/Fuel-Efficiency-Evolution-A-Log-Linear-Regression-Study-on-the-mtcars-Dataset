```markdown
# Fuel Efficiency Evolution: A Log-Linear Regression Study on the mtcars Dataset

## 1. Business Problem
Optimizing fuel consumption is a critical factor for automotive engineering and environmental policy. Using the classical `mtcars` dataset, this project aims to model fuel efficiency (**Miles Per Gallon - MPG**) by identifying the impact of vehicle power and transmission type while ensuring a statistically sound and parsimonious model.

## 2. Context
The analysis focuses on selecting a quantitative predictor (Horsepower vs. Weight) and a qualitative dummy variable (Transmission Type: Automatic vs. Manual).
* **Target Variable:** `mpg` (Miles per Gallon).
* **Predictors:** `hp` (Gross Horsepower) and `am` (Transmission: 0 = Automatic, 1 = Manual).

## 3. Analysis Assumptions
To conduct this analysis, the following Gauss-Markov assumptions were strictly tested:
* **Linearity:** The relationship between predictors and the response (or transformed response) must be linear.
* **Normality:** Residuals must follow a normal distribution (verified via Shapiro-Wilk and Anderson-Darling tests).
* **Homoscedasticity:** Constant variance of residuals (verified via Breusch-Pagan and White tests).
* **Independence:** Absence of multicollinearity (monitored via Variance Inflation Factor - VIF).

## 4. Solution Strategy
The modeling process evolved through three distinct stages:

| Step | Description |
| :--- | :--- |
| **Exploratory Analysis** | Correlation matrix and VIF analysis to choose `hp` over `wt` to preserve the significance of the transmission dummy. |
| **Simple Linear Model** | Initial OLS regression. Diagnostics revealed significant heteroscedasticity (p < 0.05 in BP test). |
| **Log-Linear Transformation** | Applying $\log(mpg)$ to stabilize variance and satisfy the homoscedasticity assumption. |
| **Sensitivity Analysis** | Identifying influential points (Maserati Bora, Cadillac Fleetwood, Lincoln Continental) using Cook's Distance ($4/n$). |
| **Refined Final Model** | Re-fitting the Log-Linear model after removing three outliers, improving the Adjusted $R^2$ from 0.76 to 0.81. |



## 5. Key Insights
* **Heteroscedasticity Correction:** The initial linear model showed a "funnel" pattern in residuals. The log-transformation successfully corrected this, with the Breusch-Pagan p-value increasing from 0.02 to 0.56.
* **Transmission Impact:** In the final model, manual transmission vehicles are, on average, **21.3%** more efficient than automatic ones of the same power.
* **Power Decay:** For every additional unit of horsepower, fuel efficiency decreases by approximately **0.3%**, holding transmission constant.
* **Outlier Influence:** Luxury and high-performance outliers (Maserati/Cadillac) were pulling the regression line, distorting the trend for standard passenger vehicles.

## 6. Technical Implementation (R Code)

### Final Refined Model fitting
```r
# Log-Linear model after removing influential observations
# Indices removed: Maserati Bora, Cadillac Fleetwood, Lincoln Continental
mtcars_refined <- mtcars[-c(31, 15, 16), ]

modelo_final <- lm(log(mpg) ~ hp + am_fator, data = mtcars_refined)
summary(modelo_final)

# Heteroscedasticity Test
library(lmtest)
bptest(modelo_final)

```

## 7. Business Results

As a result of this project, the suggested predictive framework for a standard 150 hp car is:

* **Automatic:** Expected 17.92 MPG.
* **Manual:** Expected 21.74 MPG.

*This project demonstrates how data transformations and sensitivity analysis can transform a biased operational model into a robust predictive tool.*

## 8. Next Steps

* **Interaction Effects:** Explore if the effect of horsepower on fuel efficiency differs significantly between manual and automatic transmissions.
* **Polynomial Regression:** Test for non-linear power effects (e.g., ) to see if the rate of efficiency decay changes at very high horsepower levels.
