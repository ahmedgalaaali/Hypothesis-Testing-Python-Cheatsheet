# Hypothesis Testing Python Cheatsheet

## One Sample & Two Sample z-test

### 1. Mathematical z-score and p-value extraction
#### One-Sample z-score mathematically:
$$Z_{c}=\frac{\bar{X}-\mu}{\frac{\sigma}{\sqrt{n}}}$$

#### Two-Sample z-score mathematically:
$$Z_{c} = \frac{(\bar{X}_1 - \bar{X}_2) - (\mu_1 - \mu_2)}{\sqrt{\frac{\sigma_1^2}{n_1} + \frac{\sigma_2^2}{n_2}}}$$

```python
from scipy.stats import norm
zscore = (samp_mean - mu) / (sigma / np.sqrt(n))
p_val = 2 * (1 - norm.cdf(abs(zscore)))
```

### 2. Code z-score and p-value extraction
The function `ztest()` can be used for extracting the z-score and p-value for both one-sample and two-sample z-test by changing the mentioned parameters **when the population standard diviation is known**
```python
from statsmodels.stats.weightstats import ztest
zscore, p_val = ztest(x1=sample.charges, value=mu, alternative="two-sided")
```
#### One-Sample Test:

- `x1`: The sample that we will compare to the population mean.
- `value=mu`: in one-sample test, indicates to the known population mean.

#### Two-Sample Test:

- `x1`: The first sample in two-sample test.
- `x2`: The second sample in the two-sample test.
- `value=0` (Default): A standard test to see if the two groups are just different or equal.
- `value=X`: A margin test to see if the gap between the two groups is strictly greater or less than X.

---

## One-Sample t-test

When the population mean is unknown, we use a standard student's t distribution table.

### 1. Mathematical t-score and p-value extraction

$$ t_{c}=\frac{\bar{X}-\mu}{\frac{s}{\sqrt{n}}}$$

```python
from scipy.stats import t
tscore = (samp_mean - mu) / (std / np.sqrt(n))
df = n - 1
p_value = 2 * (1 - t.cdf(abs(tscore), df))
```

### 2. Code t-score and p-value extraction

The function `ttest_1samp()` can be used for extracting the t-score and p-value **when the population standard diviation is unknown**

```python
from scipy.stats import ttest_1samp
ttest, p_val = ttest_1samp(a=data.charges, popmean=mu, alternative="two-sided")
```
- `a`: array of data points to be tested
- `popmean`: population mean
- `alternative`: type of tailed test

---

## Levene's & Two-Sample t-test
>The two-sample t-test is a test to compare between the means of two numerical groups. Before the test is perform we must conduct a levene's test, **to compare the two groups variances**.

```python
from scipy.stats import levene
lev_stats, lev_sign = levene(group1, group2)
```
**Interpretation:** if the levene's significance is:
- Less than p-value: *equal variances not assumed*.
    - **Python Action:** set `equal_var` to `Fales`
    - **SPSS Action:** Read the bottom line of the table
- Greater than p-value: *equal variances assumed*
    - **Python Action:** set `equal_var` to `Fales`
    - **SPSS Action:** Read the bottom line of the table

```python
from scipy.stats import ttest_ind
tscore, pvalue = ttest_ind(smokers_charge, non_smokers_charge, equal_var=False)
```

## Chi Square Test
>Used when dealing with categorical data / frequencies.

$$X^2=\frac{(O-E)^2}{E}$$

### 1. Goodness of Fit (one variable)
> Used to test of a set of data matcches an expected, claimed or historical distribution. **(Nonparametric)**

```python
from scipy.stats import chisquare
observed_counts = data['region'].value_counts()
total_n = len(data)
expected_counts = [total_n / 4] * 4      # 4 represents the number of unique categorical values
chi2_stat, p_val = stats.chisquare(f_obs=observed_counts, f_exp=expected_counts)
```

### 2. Test of Independence (Two Variables)
> Used to test if a 2 sets of categorical data are significantly associated.

```python
from scipy.stats import chisquare
homogeneity_table = pd.crosstab(category_1, category_2)
chi2, p_val, dof, expected = stats.chi2_contingency(homogeneity_table)
```

### Interpretation
1. SPSS first line: Is the test safe for this case?
2. Stats: $$X^2$$, p-value, df
3. look at the percentage in the cross table
