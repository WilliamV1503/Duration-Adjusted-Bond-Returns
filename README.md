# Duration-Adjusted Bond Returns

This notebook calculates duration-adjusted Treasury bond returns using zero-coupon instantaneous forward rate curves.

It builds on the methodology suggested by Binsbergen et al. ([SSRN paper](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3914422)) and introduces an adjustment:  
Instead of rolling down the Treasury curve by a duration-scaled time increment, this notebook rolls down the coupon-equivalent zero-coupon Treasury by exactly one month. This provides a more consistent and interpretable monthly return per coupon.

## Weaknesses

- Mergent and TRACE data span only from July 2002 to March 2024.
- A numerical solver is used to estimate yields from dirty prices, but some derived yields appear to be unfeasible.
- A simple time interpolation method is used to estimate dirty prices when data is missing.
- The Svensson model is used to estimate yields for maturities under one year, despite known limitations during the sample period. See: [FED Website Paper](https://www.federalreserve.gov/pubs/feds/2006/200628/200628pap.pdf).
- Current return data is calculated at a monthly frequency, which may not capture higher-frequency dynamics.

## Goals

- Implement a more accurate and robust zero-coupon Treasury curve estimator to replace the Svensson model.
- Develop a more reliable method for interpolating missing bond prices.
- Extend the methodology to produce duration-adjusted return data at daily frequency.

## References

- Binsbergen, J. H., Diamond, W. F., & Grotteria, M. (2021). *Risk-Free Interest Rates*. [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3914422)  
- Gürkaynak, Sack, & Wright (2006). *The U.S. Treasury Yield Curve: 1961 to the Present*. [FED](https://www.federalreserve.gov/pubs/feds/2006/200628/200628pap.pdf)

