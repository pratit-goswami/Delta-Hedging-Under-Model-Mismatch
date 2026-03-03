# Delta Hedging Under Model Mismatch  
## A SABR vs. Black–Scholes Study

**Author:** Pratit Goswami  

---

## Objective and Motivation

This project studies discrete delta hedging performance under volatility model misspecification. The goal is to quantify how calibration error or the use of a flat Black–Scholes volatility instead of a stochastic volatility model — translates into realized hedging error when the true dynamics follow SABR. Rather than comparing prices at inception, the focus is on full P&L distributions from dynamically hedging a short at-the-money option under competing models.

In practice, traders hedge using model-implied deltas, and small modeling errors can compound into systematic bias or amplified tail risk. Evaluating the dispersion and skew of hedging outcomes therefore provides a direct measure of model risk: not whether a model prices correctly, but whether it meaningfully controls risk.

We compare three hedging approaches:

1. **Correctly calibrated SABR deltas**
2. **Miscalibrated SABR deltas**
3. **Black–Scholes deltas with constant ATM implied volatility**

The objective is to quantify how model misspecification translates into hedging P&L dispersion and tail risk.

---

## Model Setup

The underlying forward price is modeled under the risk-neutral forward measure using the lognormal SABR framework:

$$
dF_t = \alpha_t F_t^\beta dW_1, \qquad
d\alpha_t = \nu \alpha_t dW_2, \qquad
dW_1 dW_2 = \rho dt
$$

with fixed elasticity parameter $\beta$.

The model captures stochastic volatility, skew, and smile effects observed in equity options markets. For a selected AAPL expiry, Black–Scholes implied volatilities are extracted from the option chain and the SABR parameters ($\alpha, \rho, \nu$) are calibrated via nonlinear least squares using the Hagan lognormal implied volatility approximation. The calibrated parameters define the “true” market dynamics.

An ATM call option is then priced by Monte Carlo simulation under these dynamics to ensure consistency between the pricing and hedging experiment.

---

## Methodology

Using the calibrated SABR parameters, forward paths are simulated via Euler discretization to generate terminal payoffs and dynamic hedge paths.

A short ATM call position is delta-hedged discretely (approximately daily) under three models:

- Correctly calibrated SABR  
- Deliberately miscalibrated SABR  
- Black–Scholes with constant ATM volatility  

Deltas are computed using Black-76 formulas with either Hagan-implied volatilities or constant volatility inputs. The hedge is implemented as a self-financing strategy with cash accruing at the risk-free rate. For each model, the full distribution of final hedging P&L is computed across Monte Carlo paths, allowing direct comparison of mean bias, variance, and tail risk attributable solely to model specification error.

---

## Results

The correctly specified SABR hedge produces a P&L distribution centered close to zero with the smallest dispersion, reflecting consistency between pricing and hedging under the true stochastic volatility dynamics. Introducing parameter misspecification increases both variance and tail asymmetry. The constant-volatility Black–Scholes hedge performs worst, as it fails to capture smile and volatility-of-volatility effects. Importantly, all strategies may price the option reasonably at inception, yet their risk profiles differ materially once dynamic hedging begins. This highlights that model risk manifests primarily through hedging performance rather than initial valuation error.

### Key Observations

- **SABR (true parameters):** Near-zero mean P&L, lowest standard deviation, comparatively symmetric distribution, 
- **SABR (miscalibrated):** Higher variance, mild bias, thicker tails,  
- **Black–Scholes (ATM vol):** Largest dispersion and most pronounced downside tail risk.  

---

## Conclusion

This study demonstrates that model risk is fundamentally a hedging problem rather than a pricing problem. Even when competing models produce similar option values at inception, their dynamic risk profiles can differ substantially once discrete hedging is introduced. Stochastic volatility structure and accurate calibration materially reduce P&L dispersion and tail exposure, while simplified or misspecified models amplify hedging error in economically meaningful ways. By linking volatility modeling assumptions directly to realized risk outcomes, this project emphasizes the practical importance of model choice in derivatives trading and risk management.
