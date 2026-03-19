# Mortgage Prepayment Simulation — Stochastic Interest Rates and Monte Carlo Framework

## 1. Problem Motivation

Prepayment rates are a key driver of uncertainty in the cashflows when modelling mortgages for mortgage insurance pricing or MBS pricing. This directly impacts insurance exposures, valuations, and hedging. 

From my professional experience, I realised in practice, prepayment is often modelled using deterministic economic scenarios, which fail to capture:
- sensitivity to interest rates  
- heterogeneity in borrowers' behaviour and prepayment incentive  
- uncertainty around the point-estimate prepayment rate  

This project attempts to explore a stochastic simulation framework to understand how interest rate paths and prepayment incentives jointly influence the prepayment rates at a mortgage portfolio level.

---

## 2. Objective

Use stochastic interest rate paths and a simplified version of actual borrower behaviour to simulate the evolution of a mortgage pool over a 10-year horizon. Additionally, using these simulated paths to estimate the distribution of unpaid principal balance (UPB) and prepayment rates (CPR).

---

## 3. Data & Inputs

- A Synthetic mortgage pool is developed with loan-level characteristics like:
  - Original Loan balance  
  - Original Interest rate of the loan at its origination  
  - Credit score, like FICO  
  - Original Loan-to-Value Ratio (LTV) of the loan
- Modelling parameters for interest rate paths and prepayment sensitivity  

---

## 4. Modelling Approach

### Interest Rate Modelling
- Interest rate paths are simulated using stochastic interest rate models like Vasicek and Cox-Ingersoll-Ross (CIR)
- Selection of the CIR model as the primary model for modelling interest rate paths since it does not produce negative interest rates, making prepayment incentives economical to model  
- Continuous stochastic interest rate models are discretised using the Maruyama scheme  

---

### Prepayment Model
- Prepayment likelihood is modelled using a logistic function of the spread between the original interest rate of the loan and prevailing market rate simulated from a stochastic interest rate path.
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
- Borrower characteristics like FICO and LTV introduce non-linear effects in low-rate environments
- Even under similar initial conditions, stochastic paths produce a wide range of pool balances, which highlights the limitations of deterministic scenarios

---

## 6. Limitations

- The prepayment model is simplified and fails to incorporate:
  - seasonality effects  
  - burnout behaviour  
  - macroeconomic factors like unemployment and housing turnover 
- Calibrating interest rate models to historical FED data was beyond the scope of this project  
- Correlation between borrower characteristics and macro conditions is not explicitly modelled  

---

## 8. Repository Structure

- `01_StochasticInterestRateModels` — Simulation of interest rate paths (Vasicek, CIR)  
- `02_MortgageEngine` — Loan-level amortisation and prepayment model  
- `03_SimulationEngine` — Monte Carlo simulation and aggregation of portfolio metrics  

---

## 9. Takeaway

Working on this project has helped me understand the sensitivity of mortgage portfolios to interest rates and loan characteristics, particularly around prepayment behaviour. This motivated the need for a robust and stochastic modelling framework to model prepayment uncertainties.  
