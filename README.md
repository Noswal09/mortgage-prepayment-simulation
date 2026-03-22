# Mortgage Prepayment Simulation — Stochastic Interest Rates and Monte Carlo Framework

## 1. Problem Motivation

Prepayment rates are a key driver of uncertainty in the cashflows when modelling mortgages for mortgage insurance pricing or MBS pricing. This directly impacts insurance exposures, valuations, and hedging. 

From my professional experience I realised, in practice, prepayment is often modelled using deterministic economic scenarios, which fail to capture:
- sensitivity to interest rates  
- heterogeneity in borrower's behaviour and prepayment incentive  
- uncertainty around the point-estimate prepayment rate  
- non-linear dependence on interest rate incentives

This project attempts to explore a stochastic simulation framework to understand how interest rate paths and prepayment incentives jointly influence the prepayment rates at a mortgage portfolio level.

---

## 2. Objective

To quantify how stochastic interest rate dynamics and borrower-level behavioural assumptions influence:
- Distribution of prepayment rates
- Evolution of Unpaid Principal Balance
- Dispersion of mortgage portfolio cashflows

---

## 3. Data & Inputs

- United States 30-Year Fixed Rate Mortgage Average data fetched from FRED's MORTGAGE30US database for the period January 2015 to December 2024
- Market Yield on U.S. Treasury Securities at 3-Month Constant Maturity data from FRED's DGS3MO for the period January 2015 to December 2024
- A Synthetic mortgage pool is developed with loan-level characteristics like:
  - Original Loan balance  
  - Original Interest rate of the loan at its origination  
  - Credit score like FICO  
  - Original Loan-to-Value Ratio (LTV) of the loan
- Modelling parameters for interest rate paths and prepayment sensitivity  

---

## 4. Modelling Approach

### Interest Rate Modelling
- Interest rates paths are simulated using stochastic interest rate models like Vasicek and Cox-Ingersoll-Ross (CIR)
- Selection of the CIR model as the primary model for modelling interest rate paths since it does not produce negative interest rates, making prepayment incentives economical to model  
- Continuous stochastic interest rate models are discretised using the Maruyama scheme  

---

### Calibrating Interest Rate Models
- OLS techniques used to calibrate a CIR and Vasicek model. Final calibrated model includes fixed $\theta$ parameter and other parameters being estimated using OLS
- Linear Regression fit to convert short-term interest rates into mortgage rates
- Limitations of observed rates not following a mean-reverting process leads us to chose a calibration model that fixes $\theta$ parameter

---

### Prepayment Model
- Prepayment likelihood is modelled using a logistic function of spread between the original interest rate of the loan and prevailing market rate simulated from stochastic interest rate path.
- Prepayment probability is then adjusted using borrower characteristics like:
  - Higher FICO and Lower LTV leading to a higher prepayment incentive  
  - Lower FICO and higher LTV leading to a lower prepayment incentive  

---

### Simulation Framework
- 5000 Monte Carlo paths are simulated over the 10 year period (120 months)
- Loan-level prepayment probability and loan balance are generated  
- Loan-level prepayments and balances are aggregated to generate:
  - Pool Unpaid Principal Balance over 120 months  
  - CPR distributions over 10 years
- Sensitivity testing done around the initial interest rate selected for interest rate paths (+/-100bps changes in initial interest rate checked)

---

## 5. Key Insights

- Interest rate paths are primary drivers of prepayment uncertainty, with falling rate scenarios producing higher CPR dispersion
- Borrower characteristics like FICO and LTV introduces non-linear effects in low-rate environments
- Even under similar initial conditions, stochastic paths produce a wide range of pool balances which higlights the limitations of deterministic scenarios
- The results illustrate a fundamental feature of mortgage portfolios:
  - Negative convexity — as rates fall, prepayments accelerate, limiting upside; as rates rise, prepayments slow, extending duration.
- From a valuation perspective, ignoring stochastic rate dynamics can lead to systematic mispricing of MBS cashflows. 

---

## 6. Limitations

- Prepayment model is simplified and fails to incorporate:
  - seasonality effects  
  - burnout behaviour  
  - macroeconomic factors like unemployment and housing turnover
- OLS techniques were used to calibrate the CIR model, however, the calibration can be refined further using MLE techniques
- In calibration of the CIR model, Feller's condition is not satisfied leading to rates converging to 0 in some of the paths   
- Correlation between borrower characteristics and macro conditions is not explicitly modelled  

---

## 7. Repository Structure

- `01_StochasticInterestRateModels` — Simulation of interest rate paths (Vasicek, CIR)  
- `02_CalibratingInterestRateModels` — Calibration of parameters for CIR and Vasicek models
- `03_MortgageEngine` — Loan-level amortisation and prepayment model  
- `04_SimulationEngine` — Monte Carlo simulation and aggregation of portfolio metrics  

---

## 8. Takeaway

Working on ths project has helped me understand the sensitivity of mortgage portfolios to interest rates and loan characteristics, particularly around prepayment behaviour. This motivated the need for a robust and stochastic modelling framework to model prepayment uncertainties.  

---

## Libraries

`numpy` `pandas` `matplotlib` `statsmodels` 

## How to Run

Run the notebooks in order: `01` → `02` → `03` → `04`. Notebook 04 imports functions from notebooks 01, 02, and 03 using `import_ipynb`.
