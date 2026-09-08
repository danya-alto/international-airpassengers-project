# Air-passenger forecasting 

## Recommendation

Ship **AutoETS** as the forecast model because it achieved a mean MASE of **0.952** across the eight rolling-origin validation windows, compared with **1.313** for the seasonal-naive floor. Lower MASE is better, so AutoETS provides the stronger point forecast in the final cross-validation comparison.

## What the series shows

Passenger numbers rise steadily over time and follow a clear yearly pattern, with higher traffic around the middle of the year and lower traffic near the beginning and end. The seasonal ups and downs also become larger as passenger numbers grow, so the pattern becomes more pronounced over time.

The seasonal-naive benchmark forecasts each month using the value from the same month one year earlier. This provides a useful floor because it preserves the annual pattern and gives the more complex model something meaningful to beat.

## Model comparison

The final comparison uses the same eight rolling-origin windows for AutoETS and seasonal naive, with each window forecasting the next 12 months. Each model is fitted and evaluated using only the information available at that forecast origin.

| Model          |      MASE |     RMSSE | Scaled CRPS | 80% coverage |  MASE range |
| -------------- | --------: | --------: | ----------: | -----------: | ----------: |
| **AutoETS**    | **0.952** | **1.003** |  **0.0499** |        49.0% | 0.565–1.554 |
| Seasonal naive |     1.313 |     1.246 |      0.0616 |        51.0% | 0.412–1.959 |

AutoETS beats the seasonal-naive floor on all three error measures. Its MASE is **0.952** compared with **1.313** for seasonal naive, its RMSSE is **1.003** compared with **1.246**, and its scaled CRPS is **0.0499** compared with **0.0616**. Lower values are better for all three measures, so the rolling-origin evaluation supports AutoETS over the benchmark for both the central forecast and the evaluated forecast distribution.

The MASE ranges also show that performance changes across forecast periods. AutoETS ranges from **0.565 to 1.554**, while seasonal naive ranges from **0.412 to 1.959**. This is why the recommendation is based on the full eight-window comparison rather than a single forecast period.

## Framework shortlist

AutoGluon was used to shortlist models before the final rolling-origin evaluation. Its validation scores are negative, so values closer to zero are better. It used only one validation window, which means its ranking is useful for choosing candidates but is not strong enough to determine the final recommendation by itself.

AutoETS was therefore tested again against the seasonal-naive floor using the required eight rolling windows. The final recommendation is based on this broader comparison rather than AutoGluon's single-window ranking.

## Intervals

The 80% intervals are **not well calibrated for either model**. AutoETS achieved only **49.0% coverage**, while seasonal naive achieved **51.0%**. Both are far below the intended 80% level.

This means the bands are too confident: actual passenger numbers fall outside them more often than an 80% interval should imply. AutoETS therefore has better forecast accuracy and a better scaled CRPS than the floor, but its uncertainty band should not yet be treated as an honest 80% range.

The notebook does not currently report mean interval width from the rolling-origin harness, so no numerical width is claimed here. Width should be added to the harness before making a numerical statement about how wide the intervals are.

## Residuals

The seasonal-naive residuals still contain systematic structure. The Ljung–Box test gives p-values of **2.32 × 10⁻⁴³** at lag 12 and **1.71 × 10⁻⁴⁴** at lag 24. These extremely small values show that simply repeating the previous year's value does not capture everything in the series.

In particular, the benchmark does not fully adapt to changes in the overall level and the size of the seasonal swings. This supports using a model such as AutoETS that can adapt its level, trend, and seasonal pattern rather than simply copying the previous year.

The rolling-origin results show that AutoETS handles this structure better than the floor, as reflected in its lower MASE and scaled CRPS. However, its low interval coverage shows that uncertainty remains the main weakness.

## One next change

The next step should be to **improve the calibration of AutoETS's prediction intervals** while keeping its point forecast as the starting model. The rolling-origin evaluation shows that AutoETS has the better forecast accuracy, but its **49.0% coverage** is far below the intended 80%.

After changing the interval estimation, the same eight-window rolling-origin harness should be run again. The goal would be to move coverage closer to 80% while retaining AutoETS's advantage in MASE and scaled CRPS.
