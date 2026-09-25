# Chicago Crime Trend Analysis & Forecasting

**Author:** Hala Salah

## Overview

This project analyzes more than 20 years of Chicago crime data (2001–2022) to answer stakeholder questions from a local newspaper reporter about crime trends, and forecasts monthly crime counts to help Chicago law enforcement plan resources.

The project has two parts:
1. **Exploratory time series analysis:** long-term trends, crime types moving against the trend, rush-hour patterns, and seasonality.
2. **Forecasting:** 6-month forecasts of Theft and Battery using SARIMA models.

## Data

- **Source:** [Chicago Data Portal — Crimes 2001 to Present](https://data.cityofchicago.org/Public-Safety/Crimes-2001-to-Present/ijzp-q8t2)
- **Size:** 7,713,109 crime records combined from 22 yearly CSV files
- **Cleaning:** converted dates to a datetime index, removed 47 records with a missing district (0.0006% of the data), and merged a crime category that was renamed in the source data (see the data quality note below)
- **Feature engineering:** added US holidays, year, month, hour, and day-of-week features

## Key Findings

### 1. Total crime has dropped by half

Total crime fell from **485,886** incidents in 2001 to **238,858** in 2022, a decline of about **51%**. The drop was steady until 2014, then leveled off. 2020 and 2021 were the lowest years on record, in line with the COVID-19 pandemic, followed by a partial rebound in 2022.

<img width="1044" height="502" alt="download" src="https://github.com/user-attachments/assets/c1dad079-2c20-481e-8534-39b6767a905d" />

### 2. Some crimes are rising against the trend

While total crime fell, four crime types increased (average of the first 3 years vs. the last 3 years):

| Crime Type | % Change |
|---|---|
| Weapons Violation | +105.0% |
| Deceptive Practice | +23.7% |
| Homicide | +20.8% |
| Interference with Public Officer | +15.6% |

Weapons violations more than doubled, and the rise in homicides is especially concerning given the severity of the crime.

> ⚠️ **Data quality note:** An initial analysis showed "Criminal Sexual Assault" rising by +4,254% while "Crim Sexual Assault" fell by −98.5%. These turned out to be the same crime under two labels, because the category was renamed in the source data. After merging the two labels, the true change is **−18.2%**.

### 3. The evening commute sees more crime than the morning

- The PM rush hour (4–7 PM) has **1,206,345** crimes vs. **770,650** in the AM rush hour (7–10 AM), about **57% more**.
- Theft, Battery, and Criminal Damage are the top 3 crimes in both windows. Burglary appears in the AM top 5, while Narcotics and Assault appear in the PM top 5.
- Motor vehicle thefts are about **29% more common** in the PM rush (53,714 vs. 41,578).

### 4. Crime follows clear yearly and weekly cycles

- **Yearly:** crime peaks in summer (around July) and drops in winter (around February), with a seasonal swing of about **8,900 crimes per month**.
- **Weekly:** crime is highest on **Friday** and lowest on **Sunday**, with a swing of about **96 crimes per day**.

<img width="1183" height="484" alt="download (1)" src="https://github.com/user-attachments/assets/fd7706a4-57f7-4f45-bb14-273d77a88272" />

## Forecasting (Jan–Jun 2023)

For each crime, I built a manual SARIMA model (orders chosen from ACF/PACF plots) and an `auto_arima` model, then selected the one with the best accuracy on a held-out 6-month test set.

| Crime | Selected Model | Test MAPE | Forecast Jan 2023 | Forecast Jun 2023 | Change |
|---|---|---|---|---|---|
| Theft | Manual SARIMA(1,1,1)(1,1,1,12) | 3.57% | 4,024 | 5,034 | +1,010 (+25.1%) |
| Battery | auto_arima ARIMA(1,1,1)(0,1,1)[12] | 3.09% | 2,821 | 3,781 | +959 (+34.0%) |

For Theft, `auto_arima` had a lower AIC but performed worse on the test set, so the manual model was chosen based on out-of-sample accuracy. For Battery, both models performed similarly, and the simpler `auto_arima` model was selected.

<img width="1284" height="584" alt="download (2)" src="https://github.com/user-attachments/assets/35273d32-f3a2-4017-90ee-bebd3a15e27d" />

## Recommendations

1. **Scale up police resources heading into summer 2023.** Both Theft and Battery are forecast to rise from the February low toward the summer peak.
2. **Prioritize Theft by volume.** Theft remains the larger burden, reaching about 5,034 incidents per month by June 2023.
3. **Monitor Battery closely.** It is forecast to grow faster in relative terms (+34% vs. +25%).
4. **Focus patrols on evening hours and Fridays**, when crime is most concentrated.

## Tools

Python, pandas, NumPy, Matplotlib, Seaborn, statsmodels, pmdarima, holidays

## How to Run

Open the notebook in Google Colab using the badge at the top of the notebook. The dataset is not included in this repository because of its size; download it from the Chicago Data Portal link above.
