# econ3916-lab12-ols-prediction

# OLS as a Line of Best Fit: Estimation versus Prediction

## Objective
I used the California Housing data (20,640 districts) to check that three ways of fitting OLS give the same line, and to show that a coefficient's precision and a model's test error answer different questions.

## Methodology
- Fitted the same line of median house value on median income three ways: the closed form (covariance over variance), `statsmodels`, and `scikit-learn`. I checked that all three agreed to six decimals.
- Added 0, 1, 5, 20 and 100 columns of pure noise to the regression and compared R-squared with adjusted R-squared at each step.
- Plotted the fitted line and the residuals against the fitted values to look for patterns that R-squared cannot show.
- Split the data before fitting (80% train, 20% test, fixed `random_state`). Fitted an eight-feature linear model on the training rows and scored it on both the training rows and the test rows.
- Used one specification (value on median income and rooms per household) for two jobs: an `AveRooms` coefficient with its 95% confidence interval, and a test RMSE.
- Wrote a function, `holdout_rmse(data, features)`, that splits the data the same way, fits on the training rows and returns the test RMSE. I used it to score median income alone.
- For the take-home challenge, I counted the districts at the 5.0 ceiling in the house value column, dropped them, and refitted the eight-feature model.

## Key Findings
- **Three routes, one line.** The closed form, `statsmodels` and `scikit-learn` all gave an intercept of 0.450856 and a slope of 0.417938.
- **R-squared rises with noise.** R-squared was 0.4734 with no noise columns and 0.4758 with 100. Adjusted R-squared went from 0.4734 to 0.4732, so it did not rise.
- **Residual plot.** The right-hand panel shows two problems that the R-squared of 0.473 hides: the residuals fan out (unequal spread), and the house values are capped at the top, which shows up as a straight diagonal edge.
- **Train versus test.** The eight-feature model had a train RMSE of 0.7197 (R-squared 0.6126, n = 16,512) and a test RMSE of 0.7456 (R-squared 0.5758, n = 4,128). The gap is small.
- **Estimation and prediction.** The `AveRooms` coefficient was -0.0381, with a 95% CI of [-0.0430, -0.0333]. The same specification had a test RMSE of 0.8379. A tight interval that excludes zero does not mean the model predicts well.
- **Median income alone.** Test RMSE was 0.8421, compared with 0.7456 for all eight features.
- **Challenge: the 5.0 ceiling.** 965 of 20,640 districts (4.7%) sit at the ceiling. Dropping them shrank most of the coefficients, by about 5% to 27% (for example, `MedInc` went from 0.4487 to 0.4260). Test RMSE fell from 0.7456 to 0.6511. The lower error does not mean a better model: the hardest, most expensive districts are gone from the test rows too. The refitted model describes only districts below the ceiling, and dropping the capped rows assumes they don't matter for the relationship, which isn't true.
