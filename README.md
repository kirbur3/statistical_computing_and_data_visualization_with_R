# statistical_computing_and_data_visualization_with_R

## Repository Overview
Four R projects from STAT363 (Statistical Computing and Data Visualization) at Portland State University, plus one personal project and one additional course project. Together they cover a range of practical data skills: wrangling and reshaping large time series datasets, building multi-layered visualizations in both base R and ggplot2, simulation-based statistical reasoning through the CLT and Monte Carlo methods, and independent exploratory analysis on self-selected data. All coursework is written in R Markdown.

Projects in this repo:
- **Data Cleaning and Time Series Analysis**
- **Data Visualization and Statistical Analysis in R**
- **Central Limit Theorem Demonstration and Monte Carlo Estimation of Pi**
- **Most Watched Movies and TV Shows Personal Project**

---

## Data Cleaning and Time Series Analysis

Note: This project pulled live data from the Johns Hopkins University CSSE COVID-19 GitHub repository. That dataset is no longer actively updated, so the original live pull is not reproducible, but the full code and methodology are included.

This project is listed on my resume as "Data Cleaning and Time Series Analysis."

Course project on COVID-19 data wrangling and visualization in R.

Tools: R, tidyverse, dplyr, tidyr, lubridate, stringr, ggplot2, gridExtra, ggthemes

### Data Wrangling
- Pulled county-level confirmed case and death time series (1.6M+ rows, wide format: one column per date) from the JHU CSSE GitHub repository
- Reshaped both files from wide to long format using tidyr's pivot_longer() with lubridate::mdy() for date parsing, then aggregated from county to state level using dplyr group_by() and summarise()
- Joined the reshaped case/death data with state-level vaccination time series using dplyr's full_join(), aligning on state and date; filtered to start at January 1, 2021
- Used stringr's str_subset() to extract mask mandate, vaccination policy, and social distancing mentions from unstructured state policy tracker text for California and Oregon

### Analysis and Visualization
- Selected 3 highly impacted states (CA, FL, NY) and 3 mildly impacted states (HI, OR, RI) based on confirmed case counts from covid.usa.daily
- Built ggplot2 visualizations (with gridExtra for multi-panel layout) comparing confirmed cases, deaths, and people fully vaccinated across all 6 states from January 2021 to May 2022
- 90-day window analysis: compared average death counts per state across three vaccination rollout periods (Jan-Mar, Apr-Jun, Jul-Sep 2021) alongside average vaccination counts, summarized in data frames and visualized with ggplot2 point plots
- Independent exploration: extracted and compared Case_Fatality_Ratio from covid.usa.daily across all 6 states; New York had the highest ratio

---

## Data Visualization and Statistical Analysis in R

Exploratory visualization project using the World Resources Institute's global power plant database. Data loaded from global_power_plant_database.csv; rows with NAs in country, name, capacity_mw, or primary_fuel removed before analysis.

Tools: R, ggplot2, dplyr, base R

### Question
Which energy source in the US has the most power plants capable of generating at least 3,000 megawatts of capacity?

### Approach
- Filtered dataset to US plants (country == "USA") and explored the full capacity distribution before applying the 3,000 MW threshold
- Built two base R boxplots of capacity_mw ~ primary_fuel: one with ylim = c(0, 8000) to show the full distribution and identify which high-capacity plants are outliers relative to each fuel type, and a second clipped to ylim = c(3000, 8000) to zoom in on plants meeting the threshold
- Both base R plots include a manually specified legend using legend() covering all 14 fuel types
- Built a ggplot2 scatter plot with facet_grid(~primary_fuel) and angled x-axis labels, filtered to country == "USA" & capacity_mw >= 3000, to clearly separate plants by energy source and confirm counts

### Finding
Coal has the most US power plants at or above 3,000 MW (5 plants), followed by Gas and Nuclear (2 each) and Hydro (1). Despite having only one plant in this range, Hydro had the highest individual capacity of the group.

Extra Files: **global_power_plant_database.csv**

---

## Central Limit Theorem Demonstration and Monte Carlo Estimation of Pi

Two-part simulation project in R exploring the Central Limit Theorem through repeated sampling and Monte Carlo integration through geometric probability.

Tools: R, base R (rnorm, runif, rbinom, replicate, hist)

### Part 1 - Central Limit Theorem
- Simulated four populations of N = 10,000 from distinct distributions: Normal (mean = 5, sd = 4), Uniform(0, 10), Bernoulli(p = 0.2), and a bimodal mixture (0.3 x N(mean = 2, sd = 4) + 0.7 x N(mean = 8, sd = 1)) using rnorm(), runif(), and rbinom()
- Wrote sample.mean(pop, n, out.sample): draws n samples from a population vector and returns either the sample mean alone or a named list with the sample and the mean depending on the out.sample boolean
- Wrote samp.dist.mean(pop, n, K): calls sample.mean() K times in a loop and returns a vector of K sample means, building up the empirical sampling distribution
- Generated K = 5,000 experiments at n = 30, 50, 100, and 1,000 for each of the four populations, storing results in 5000 x 4 matrices (ExpPop.1 through ExpPop.4), one column per sample size, one row per experiment
- Visualized the sampling distributions across sample sizes to show convergence toward a Normal distribution regardless of the underlying population shape, confirming the CLT empirically

### Part 2 - Monte Carlo Estimation of Pi
- Wrote pi.MC(r, n): samples n 2D points uniformly from a bounding square of side 2r, counts how many fall inside the inscribed circle (x^2 + y^2 <= r^2), and returns 4 x (points in circle / points in square) as the MC estimate of pi
- Set r = 1 and ran K = 10,000 independent estimates at n = 100, 500, and 1,000 using replicate(); stored results in a 10000 x 3 matrix
- Plotted the sampling distribution of the MC estimator at each n as a histogram with freq = FALSE; distributions visibly narrowed as n increased, with variance consistent with the theoretical pi(4 - pi)/n, confirming CLT convergence for the MC estimator

---

## Most Watched Movies and TV Shows Personal Project

Personal data exploration project on a Kaggle dataset (flixpatrol.xlsx) of Netflix's most-watched movies and TV shows. Built to practice the full R analysis workflow (data cleaning, filtering, counting, and visualization) on a dataset I was personally interested in, with a parallel Power BI version built separately.

Tools: R, ggplot2, dplyr, readxl

### Data Preparation
- Read in .xlsx file using readxl; removed missing values with na.omit()
- Noted and documented a data quality issue: two titles ("Ginny & Georgia" and "Outer Banks") appeared as duplicate rows with different watch times, flagged in observations throughout rather than silently dropped

### Analysis
- Top 10 overall, top 20 TV shows, and top 20 movies: filtered by Type using dplyr, printed with head()
- Top 10 by genre (Comedy, Drama): filtered by Genre; noted that all top 10 Drama entries and most top 10 Comedies premiered in 2023, while Dramas skewed older
- Premiere year with fewest popular releases: base R scatter plot of Premiere vs. Watchtime to identify the low-density range, then nrow() counts per year for 1940-1959; 11 years returned 0 entries
- Premiere year with most popular releases: ggplot2 density plot to identify the peak range (2017-2020), then nrow() counts for each year; 2019 had the highest count at 1,706
- Genre distribution overall, movies only, and TV shows only: ggplot2 bar charts with angled x-axis labels; Comedy is the top genre overall and for movies, Animation leads for TV shows, and "Biography" and "News" tied as the least popular TV show genres

Extra Files: **flixpatrol.xlsx**
