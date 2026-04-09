# R Code

## Stationarity Transformations

```r
# Remove quarterly seasonality
X <- diff(UKgas, lag = 4)
adfTest(X, 'nc', lag = 5)

# Output
# ADF p-value ≈ 0.27  → still non-stationary

# Stabilize variance with log
X2 <- diff(log(UKgas), lag = 4)
adfTest(X2, 'nc', lag = 5)

# Output
# ADF p-value ≈ 0.16  → improvement but not yet stationary

# Final stationary series
X3 <- diff(diff(log(UKgas)), lag = 4)
adfTest(X3, 'nc', lag = 5)

# Output
# ADF p-value ≈ 0.01  → stationary series achieved
```

---

## Model Identification

```r
par(mfrow=c(1,2))
acf(X3, lag.max=48)
pacf(X3, lag.max=48)

auto.arima(log(UKgas), trace=TRUE, approximation=FALSE)

# Output
# Best model selected: SARIMA(1,0,0)(0,1,0)[4]
```

---

## Fit & Diagnostics

```r
UKgas.fit <- arima(log(UKgas),
                   order=c(1,0,0),
                   seasonal=list(order=c(0,1,0), period=4))

checkresiduals(UKgas.fit)

# Output
# Ljung–Box p-value > 0.05
# Residuals show no significant autocorrelation (white noise)
```

---

## Forecasting 6 Years Ahead

```r
UKgas.forecast <- forecast(UKgas.fit, h = 24)
plot(UKgas.forecast)

# Output
# 24-quarter forecasts preserving seasonal oscillations
# Confidence intervals remain stable after seasonal differencing
```

---

## Holt–Winters Comparison

```r
UKgas.fit.HW <- HoltWinters(log(UKgas))
UKgas.forecast.HW <- forecast(UKgas.fit.HW, h=24)
plot(UKgas.forecast.HW)

# Output
# Holt–Winters trend closely matched SARIMA pattern,
# supporting robustness of final forecasts
```
