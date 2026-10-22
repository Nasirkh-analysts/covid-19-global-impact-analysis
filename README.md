COVID-19 Global Impact Analysis

A Python/Pandas case study analyzing COVID-19 confirmed cases, deaths, and recoveries across 193 countries and regions, from January 22, 2020 to May 29, 2021.

Dataset

One Excel file with three sheets (Johns Hopkins CSSE format). Each sheet holds daily cumulative totals in wide format, with one column per date:

Sheet	Rows	Columns
Confirmed cases	276	4 location columns + 494 dates
Deaths	276	4 location columns + 494 dates
Recovered	261	4 location columns + 494 dates

Rows are geographic entries, not countries: 276 entries cover 193 distinct countries/regions, because countries like the US, Canada, and China are split into provinces or states. Some locations have no recovery data, so 16 locations show missing Recovered values after merging.

Location columns: Province/State, Country/Region, Lat, Long.

Objective

Practice real-world data analysis with Pandas: cleaning, reshaping, merging, and deriving insights from messy time-series data, while answering a structured set of questions about how the pandemic progressed across countries.

What I Did
Loaded and explored all three datasets: shape, data types, and cumulative case curves for the top countries and China.
Cleaned the data: forward-filled missing time-series values and replaced blank provinces with "All Province".
Analyzed individual countries: peak daily new cases (Germany, France, Italy), recovery rates (Canada vs. Australia), and death rates across Canadian provinces.
Reshaped the data from wide to long format with pd.melt(), turning 494 date columns into one Date column (one row per location per day).
Merged confirmed, deaths, and recovered into one dataset, joined on Province/State, Country/Region, and Date.
Aggregated by month and compared countries: death rates, recovery-to-death ratios, and daily new cases and deaths (using .diff() on the cumulative totals).
Key Findings
Largest totals as of May 29, 2021: the US, India, and Brazil had the most confirmed cases (33.3M, 27.9M, 16.5M). The US, Brazil, and India had the most deaths (594K, 461K, 326K).
Daily deaths: the US averaged about 1,205 new deaths per day, roughly 1.3x Brazil's ~935 and nearly double India's ~661.
France had the biggest single-day surge among Germany, France, and Italy: 117,900 new cases on April 11, 2021. Germany peaked at 49,044 (Dec 30, 2020) and Italy at 40,902 (Nov 13, 2020).
Recovery rates need context. Canada's rate (84.5%) beat Australia's (79.4%) on Dec 31, 2020. But Canada had about 20x more confirmed cases (584,409 vs. 28,425), so its higher rate mostly reflects more time for cases to resolve, not necessarily better management.
Canadian provinces: Quebec had the highest death rate (3.0%). Northwest Territories and Prince Edward Island were lowest (0.0%). Cruise-ship and repatriation entries were excluded.
Highest average daily death rate in 2020: Yemen (~25%), Sudan (~10%), and Italy (~9.7%). Death rate here is deaths divided by confirmed cases, so it also reflects how much testing a country did. A high value can mean under-testing, not just a deadlier outbreak. The average also includes early days with very few confirmed cases, which inflates it: counting only days with at least 1,000 confirmed cases, Yemen and Italy stay on top but Sudan drops out.
South Africa: about 27.6 recoveries for every death (1.55M recovered vs. 56K deaths).
US recovery ratio rose from 3.7% (March 2020) to a peak of 39.4% (October 2020). It then shows 0% because the source stopped reporting US recoveries (see below).
Data Quality Issues Found & Handled
Corrupted date headers: in each sheet, 192 of the 494 date headers had been silently converted by Excel into wrongly interpreted dates (day and month swapped). This produced impossible results, such as a "peak" in November 2021 in a dataset that ends in May 2021. I fixed it by rebuilding the date axis with pd.date_range() instead of trusting the raw headers.
Non-country entries: Diamond Princess, MS Zaandam, Grand Princess, and Repatriated Travellers appear alongside real countries. They caused division-by-zero (inf) death rates, so I excluded them from rankings.
A many-to-many merge bug: merging on Country/Region + Date alone grew the result from the expected 136,344 rows to over 21 million, because countries with several provinces matched every province against every other. Adding Province/State to the join key fixed it.
US recovery data gap: the source stops reporting US recoveries after December 13, 2020, so every later value is 0. This is a source limitation, not a calculation error.
Negative daily value: France's cumulative count was revised down by about 349,000 on May 20, 2021, which produces a negative "daily new cases" value. It does not affect the peak analysis.
Limitations
Monthly totals are slightly understated. The monthly aggregation applies .diff() inside each country-month group, which drops each month's first-day increase. For the US this gives 32.2M new cases instead of the true 33.25M (about 3% low). Calculating the daily difference per country first, then summing by month, gives exact totals.
Cumulative data can be revised, as the France example shows, so single-day figures should be read with care.
Tools Used

Python, Pandas, NumPy, Matplotlib

Project Structure
├── Covid_casestudy.ipynb                 # Full analysis notebook
├── covid_19_dataset-1718175635.xlsx      # Source data (3 sheets: confirmed, deaths, recovered)
└── README.md
How to Run
Clone this repository
Install dependencies: pip install pandas numpy matplotlib openpyxl
Open Covid_casestudy.ipynb in Jupyter Notebook and run all cells

*Built as part of my Data Analyst portfolio to practice end-to-end data cleaning, reshaping, and analysis with Pandas.*
