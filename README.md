# Delta Hedging Under Model Mismatch: A SABR vs. Black–Scholes Study

**Author:** Pratit Goswami  

---

## Objective and Motivation

This project studies **delta hedging under model mismatch** using live **AAPL options data**. The main objective is to compare hedge performance under two different volatility-model assumptions: **Black-Scholes**, which assumes volatility stays constant, and **SABR**, which allows volatility to change over time and across strikes. By placing these two models side by side, the project aims to show how model choice can affect hedging performance in practice.

The motivation for this work comes from the fact that real option markets usually show a **volatility smile or skew**, which is not captured well by the standard Black-Scholes framework. This matters because hedging decisions are often based on model outputs such as delta, and if the model does not reflect market behavior well, hedge performance may suffer. The project is especially focused on **out-of-the-money put options**, since these contracts are often more sensitive to downside moves and changing volatility conditions.

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

An OTM put option is then priced by Monte Carlo simulation under these dynamics to ensure consistency between the pricing and hedging experiment.

---

## Methodology

The workflow begins by collecting the live option-chain data, identifying a suitable expiry, and extracting implied volatilities across strikes. These market volatilities are used to build the observed put smile. After that, the **SABR model is calibrated** to the smile by fitting its parameters so that its implied volatilities match the market data as closely as possible. In parallel, the Black-Scholes setup uses the implied volatility of the selected OTM put as its flat volatility input.

Once the models are defined, the project uses **Monte Carlo simulation** to generate stock-price paths under the selected framework. At each rebalance step, the option hedge is updated using the model delta, and the cash account and hedge position are tracked over time. At maturity, the final hedging **P\&L** is calculated for each simulated path. The results are then summarized using risk and performance measures such as **mean P\&L, standard deviation, and downside tail metrics**, which allow the two hedging approaches to be compared in a clear and practical way.

---

## Results

The results show that hedge performance depends on several factors, including the volatility model, the option strike, the maturity, and the rebalancing frequency. In the AAPL OTM put scenarios tested in this project, the **Black-Scholes** and **SABR-based** hedges often produced similar average P\&L values, but differences appeared in the spread of outcomes and in measures of downside risk. This suggests that even when average performance looks close, the model used for hedging can still affect the stability of results.

### Hedging P\&L Comparison
| Metric | SABR Delta (OTM Put) | BS Flat-Vol Delta (OTM Put) |
|---|---:|---:|
| Mean | 0.398877 | 0.410757 |
| Std. Dev. | 6.091599 | 5.923109 |
| 5th Percentile | -8.602132 | -8.908358 |
| Median | 1.484764 | 1.445584 |
| 95th Percentile | 5.587819 | 5.752187 |

The experiments also showed that model differences become easier to see when the option is moved further out of the money or when the maturity is extended. In those cases, the option becomes more sensitive to changes in volatility and skew, which makes the limitations of a flat-volatility model more noticeable. Overall, the results support the idea that hedging performance should not be judged only by average P\&L, but also by how the model affects **volatility of hedge outcomes and tail losses**.

---

## Conclusion

This project provides a practical example of how option hedging can be studied using **live market data, volatility-model calibration, and Monte Carlo simulation**. It shows that while **Black-Scholes** remains a useful benchmark because of its simplicity and wide use, a more flexible model such as **SABR** can offer additional insight when market volatility is not flat across strikes. This is especially relevant for contracts such as OTM puts, where volatility skew plays a larger role in option pricing and hedge behavior.

The main takeaway is that hedge quality depends not only on the hedge formula itself, but also on the volatility model, the option setup, and the rebalancing design. This makes the project a useful introduction to **quantitative finance, derivatives modeling, and risk analysis in Python**. It also creates a strong foundation for future extensions, such as testing deeper OTM options, different hedge frequencies, transaction costs, or alternative stochastic-volatility models.
