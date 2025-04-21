# Duration-Adjusted Bond Returns

## Corporate Bond Duration-Matched Treasury Pricing Notebook
This notebook calculates corporate bond prices and corresponding duration-matched Treasury prices. The sample is constructed from merging transaction data from TRACE with bond issue data from Mergent (FISD). The sample of transactions spans from July 2002 - March 2024. 

The framework presented in the notebook currently supports corporate bullet bonds satisfying the filtering criteria suggested by van Binsbergen, Nozawa, & Schwert (2023). The methodology proposed by van Binsbergen & Schwert (2021) for pricing duration-matched synthetic Treasuries is also implemented. The Nelson-Siegel-Svensson instantaneous forward zero-coupon yield curve model and parameters published by Gürkaynak, Sack, & Wright (2006) are used to discount the cash flows for the synthetic Treasuries. The ISDA 30U/360 US day count convention is used for corporate bonds and the ISDA Actual/Actual day count convention is used for synthetic Treasuries. 

Although transaction data is at the day-level, the framework uses continuous discounting to accomodate potential intraday implementations. Initially, a continuously compounded interest accrual method was used. However, a linear (street convention) interest accrual function best reduced the error between dirty prices (implied by TRACE reported yields or estimated from clean price + accrued interest) and yields (reported by TRACE or extracted from estimated dirty prices). The best errors were as follows (analysis also included in notebook):
- Dirty Price Mean Absolute Error: $0.88
- Dirty Price Median Absolute Error: $0.18
- Yield Mean Absolute Error: 27.00 bps
- Yield Median Absolute Error: 0.35 bps

2% of the sample had absolute yield errors above the mean absolute yield error, and 6% of the sample had absolute dirty price errors above the mean absolute dirty price error. The extreme dirty prices (ex: $3) and yields (ex: 99999%) from the original sample were among the problematic data points. Among the companies with the above average yield or dirty price absolute errors, the top 10 of each included Albertsons Inc., Anheuser Busch Cos Inc., Weingarten Rlty Invs, Delta Airlines, Sara Lee Corp, and Tribune Co New. These are notable since they are overrepresented among the problematic data points but not in the original sample, unlike companies such as International Lease Fin Corp or Disney Walt Co which are overrepresented among both the original sample and the problematic sample. Aside from potential TRACE reporting errors or ignored special characteristics of these bond issues within the framework, no discernable reason for this error skew has been determined.

### Set Up:
1) Download the entire TRACE Enhanced Clean transaction data and Mergent (FISD) Bond Issue data CSV files from WRDS.
2) Upload both files ("trace_enhanced_clean_data.csv" & "fisd_bond_issue_data.csv") into a folder ("raw_data_folder") in the same directory as the notebook's .ipynb file and run all cells in the notebook in order

### Weaknesses

- Estimated dirty prices appear to slightly underestimate dirty prices implied by the TRACE reported yields.
- Outliers cause the mean absolute errors for both dirty prices and yields to be considerably larger than the median absolute errors.
- more than 99% of the estimated dirty prices allowed for the yield to be solved for with the root scalar solver, however for ~300 estimated dirty prices, severe over-estimation relative to the TRACE yield implied dirty prices caused the root scalar solver to fail, necessitating the minimize scalar solver. The latter solver used a squared error function. Still, even though the latter solver is more prone to blown-up yield values, the mean absolute error for yields did not decrease considerably after removing those data points from the sample, indicating that inaccuracies still occur with the root scalar solver implementation.
- The Svensson model is used to estimate yields for maturities under one year, despite known numerical limitations of that model for those maturities. Due to this, bounds for extreme values were set to avoid under/overflow.
- The current day count convention rule implementations may be inaccurate in general, or may not coincide exactly with every bonds's specified day count rules.
- The current pricers only accomodate bullet bonds.
- Daily data for any bond issue is sparse.

### Goals

- Create a daily-updated zero coupon corporate credit curve for each issuer to interpolate daily dirty prices.
- Implement a zero-coupon Treasury curve model more robust under the 1 year to maturity range to replace the Svensson model. 
- Include a pricer for callable bonds using additional Mergent data.
- Extend the framework to allow for intraday pricing and curve updates at a given time bin level

## References
- van Binsbergen, Nozawa, & Schwert (2023). *Duration-Based Valuation of Corporate Bonds*. [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3914422)
- van Binsbergen & Schwert (2021). *Duration-Based Valuation of Corporate Bonds*. [UPenn](https://rodneywhitecenter.wharton.upenn.edu/wp-content/uploads/2021/10/BinsbergenSchwert_2021_WP-Duration-based-valuation-of-corporate-bonds.pdf)
- Gürkaynak, Sack, & Wright (2006). *The U.S. Treasury Yield Curve: 1961 to the Present*. [FED](https://www.federalreserve.gov/pubs/feds/2006/200628/200628pap.pdf)

