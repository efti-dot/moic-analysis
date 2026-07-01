# moic-analysis
Python project to analyze how installment payment collections build over time across different loan origination cohorts, using the MOIC (Multiple on Invested Capital).
Files


moic_analysis.ipynb — full notebook (Colab-ready), already executed with outputs
moic_analysis.py — same logic as a plain Python script
data/raw_installments.csv — original raw dataset
output/cleaned_installments.csv — cleaned dataset after preprocessing
output/cohort_summary.csv — cohort summary table (Cumulative MOIC by month-on-book)
output/moic_curve.png — final vintage MOIC curve chart


How to run

Google Colab: open moic_analysis.ipynb, run the upload cell, select
raw_installments.csv, then run all remaining cells.

Locally:

bashpip install pandas numpy matplotlib
python moic_analysis.py

Approach


Cleaning

Stripped whitespace from column names
Removed commas from numeric fields (Total Advance, Total EMI, Payment Amount) and converted to numeric
Found that ~4,979 rows (42%) recorded Payment Amount as "-" — interpreted these as missed/unpaid scheduled installments and treated them as 0 rather than dropping the rows, since they reflect real (non-)payment behavior
Converted date columns to datetime
Removed 638 exact duplicate rows
Removed 3 rows where the payment date occurred before the loan's origination date (invalid records)
Removed 3 isolated single-loan cohorts (2025-09, 2025-10, 2026-01) that fell far outside the main Jan–Dec 2024 origination range and are almost certainly date-entry errors (one even had a payment date in 2027)



Cohort construction

Cohort = origination month (YYYY-MM), derived from Origination Date
Elapsed Month = number of full calendar months between Origination Date and Payment Date



MOIC calculation

Cohort Total Advance = sum of Total Advance across each loan's unique Asset ID within a cohort (not summed per payment row, to avoid double-counting)
Payment MOIC (period) = payments collected in that elapsed month ÷ cohort total advance
Cumulative MOIC = running cumulative payments ÷ cohort total advance



Cohort summary table

Pivoted: rows = cohort, columns = elapsed month, values = Cumulative MOIC
Forward-filled across months so MOIC stays flat once a cohort's payments taper off



Visualization

One line per cohort, X-axis = Months on Book, Y-axis = Cumulative MOIC
