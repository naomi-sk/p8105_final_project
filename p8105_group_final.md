p8105_group_final
================
Shayne Estill (ske2118) , WooJin Shin (ws2721), Naomi Simon-Kumar
(ns3782), Serena Ting (sjt2164)

- Detail how you completed your project
- Cover data collection and cleaning, exploratory analyses, alternative
  strategies, descriptions of approaches, and a discussion of result
- Document any changes and the reasons
- One report document per group (include all group member names)

## Motivation:

Provide an overview of the project goals and motivation.

## Related work:

Anything that inspired you, such as a paper, a web site, or something we
discussed in class.

## Initial questions:

What questions are you trying to answer? How did these questions evolve
over the course of the project? What new questions did you consider in
the course of your analysis?

## Data:

Source, scraping method, cleaning, etc.

## Exploratory analysis:

Visualizations, summaries, and exploratory statistical analyses. Justify
the steps you took, and show any major changes to your ideas.

## Additional analysis:

If you undertake formal statistical analyses, describe these in detail

## Discussion:

What were your findings? Are they what you expect? What insights into
the data can you make?

As this will be your only chance to describe your project in detail,
make sure that your report is a standalone document that fully describes
your process and results.

We also expect you to write high-quality code that is understandable to
an outside reader. Coding collaboratively and actively reviewing code
within the team will help with this!

------------------------------------------------------------------------

## P8105: Group Proposal Submission

Shayne Estill (ske2118) , WooJin Shin (ws2721), Naomi Simon-Kumar
(ns3782), Serena Ting (sjt2164)

#### Tentative Project Title

The impact of NYC Environmental Determinants on Children’s Health

#### Motivation for this project

Children’s health and development is impacted by the surrounding
environment, particularly in dense urban environments like New York City
(NYC). Poor air quality due to urban air pollution can influence and
have long-term consequences for child and adolescent respiratory health.
The challenges of urban environmental health are further complicated by
climate change, particularly through the urban heat island effect, which
can increase the risk of heat-related illnesses (Heaviside et al.,
2017). Limited research shows that elevated temperatures due to the
urban heat island effect are associated with increased emergency
department (ED) visits among children (Sheffield et al., 2018). These
health outcomes may be influenced by the availability of green spaces,
which can help filter air pollutants and provide cooler areas for
shelter from heat. Importantly, environmental health risks
disproportionately affect children from low-income neighborhoods and
communities of color, where there is often limited access to green
spaces and higher exposure to environmental stressors.

Through this project, we aim to investigate how these interconnected
environmental determinants- air quality, green space availability, and
urban heat patterns - vary across NYC boroughs and their potential
associations with children’s health, specifically focusing on
respiratory outcomes and ED visits. We believe that understanding these
relationships is crucial for informing urban planning policies and
public health initiatives aimed at improving children’s health in New
York City.

#### Anticipated Data Sources

- Environmental data:

New York City Air Quality (Kaggle:
<https://www.kaggle.com/datasets/sahityasetu/new-york-city-air-quality>)

NOAA Weather Data for temperature
(<https://www.ncei.noaa.gov/metadata/geoportal/rest/metadata/item/gov.noaa.ncdc:C00861/html>)

NYC Parks and Recreation Data (<https://www.nycgovparks.org/about/data>)

- Health and demographic data:

For ED visits and respiratory health outcomes
(<https://a816-health.nyc.gov/hdi/epiquery/visualizations?PageType=ts&PopulationSource=Syndromic&Topic=1&Subtopic=39>)

#### Intended final product

The intended final project will be a form of an interactive webpage
presenting our research findings, summarizing the associations between
environmental factors and pediatric health outcomes across NYC boroughs.
We intend to include the following sections:

1.  Interactive Visualizations:

- Air pollution distribution maps by borough

- Urban heat island effect maps by borough

- Green space accessibility visualization

- Comparative visualizations of environmental determinants and
  demographic characteristics by borough

2.  Statistical Analysis:

- Linear regression models examining associations between:

- Air quality and respiratory health outcomes stratified by demographic
  groups

- Temperature patterns and ED visits stratified by demographic groups

- Analysis of effects of green space availability

3.  Discussion of Future Directions for research and practice - in the
    form of brief recommendations for city planners and public health
    officials

#### The planned analysis / visualization / coding challenges

There may be some challenges with missing data and merging data between
different data sources, particularly when working collaboratively and in
parallel with our team. The main analytical challenge will be building
appropriate linear models to explore the effects of multiple
environmental variables (air quality, temperature, green space) on
health outcomes with the data available to us, while accounting for
relevant demographic factors.

Our planned analyses:

1.  Data Wrangling: Clean, tidy, and merge environmental, health, and
    demographic datasets

2.  Visualization (exploratory analysis)

    1.  Descriptive data visualization using basic plots

    2.  Simple maps using borough boundaries

    3.  Paneled plots to compare boroughs

    4.  Interactive dashboard using flexdashboard

3.  Statistical Analysis

    Linear models to test relationships between:

    1.  Air quality and respiratory health outcomes

    2.  Temperature and ED visits

    3.  Effects of greenspace

#### The planned timeline

November 11-15 tidy the data for analysis

November 18-22 Finish the analysis and write the report

November 25-29 finish the report and build a webpage

~ December 6th Finish the rest (screencast, Peer assessment)

#### References

Heaviside, C., Macintyre, H., & Vardoulakis, S. (2017). The urban heat
island: implications for health in a changing environment. Current
environmental health reports, 4, 296-305.

Sheffield, P. E., Herrera, M. T., Kinnee, E. J., & Clougherty, J. E.
(2018). Not so little differences: variation in hot weather risk to
young children in New York City. Public health, 161, 119–126.
<https://doi.org/10.1016/j.puhe.2018.06.004>

##### Data Cleaning: Pediatric ED Admissions and Hospitalizations data

``` r
# Load libraries 

library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
# Load and clean data for 2016-2018

ed_2016_2018 <- read_csv("ed_data_csv.csv", skip = 2) %>%
 janitor::clean_names() %>%
 filter(
   data_years == "2016-2018",
   indicator %in% c(
     "Asthma emergency department visit rate per 10,000 - aged 0-17 years",
     "Asthma hospitalization rate per 10,000 - aged 0-17 years"
   ),
   county %in% c("New York", "Queens", "Richmond", "Kings", "Bronx")
 ) %>%
 select(data_years, county, zip_code, indicator, zip_code_rate) %>%
 mutate(
   indicator = case_when(
     indicator == "Asthma emergency department visit rate per 10,000 - aged 0-17 years" ~ "ed_visits_rate",
     indicator == "Asthma hospitalization rate per 10,000 - aged 0-17 years" ~ "hosp_rate"
   ),
   zip_code_rate = case_when(
     str_detect(zip_code_rate, "\\*") ~ NA_character_,
     zip_code_rate == "s" ~ NA_character_,
     TRUE ~ zip_code_rate
   )
 ) %>%
  rename(borough = county) %>%
  mutate(
    borough = case_when(
      borough == "New York" ~ "Manhattan",
      borough == "Kings" ~ "Brooklyn",
      borough == "Richmond" ~ "Staten Island",
      TRUE ~ borough
    )
  )
```

    ## Rows: 44145 Columns: 16
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (13): Component, Indicator, Data Years, Region, County, Numerator (3-yea...
    ## dbl  (3): ZIP Code, Region Rate, State Rate
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# Load and clean data for 2019-2021

ed_2019_2021 <- read_csv("ed_data_csv.csv", skip = 2) %>%
 janitor::clean_names() %>%
 filter(
   data_years == "2019-2021",
   indicator %in% c(
     "Asthma emergency department visit rate per 10,000 - aged 0-17 years",
     "Asthma hospitalization rate per 10,000 - aged 0-17 years"
   ),
   county %in% c("New York", "Queens", "Richmond", "Kings", "Bronx")
 ) %>%
 select(data_years, county, zip_code, indicator, zip_code_rate) %>%
 mutate(
   indicator = case_when(
     indicator == "Asthma emergency department visit rate per 10,000 - aged 0-17 years" ~ "ed_visits_rate",
     indicator == "Asthma hospitalization rate per 10,000 - aged 0-17 years" ~ "hosp_rate"
   ),
   zip_code_rate = case_when(
     str_detect(zip_code_rate, "\\*") ~ NA_character_,
     zip_code_rate == "s" ~ NA_character_,
     TRUE ~ zip_code_rate
   )
 ) %>%
  rename(borough = county) %>%
  mutate(
    borough = case_when(
      borough == "New York" ~ "Manhattan",
      borough == "Kings" ~ "Brooklyn",
      borough == "Richmond" ~ "Staten Island",
      TRUE ~ borough
    )
  )
```

    ## Rows: 44145 Columns: 16
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (13): Component, Indicator, Data Years, Region, County, Numerator (3-yea...
    ## dbl  (3): ZIP Code, Region Rate, State Rate
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

The data cleaning process involved multiple steps to prepare
asthma-related health data across two time periods (2016-2018 and
2019-2021) for analysis. Data was obtained from the New York State
Asthma Dashboard, which compiles information from multiple sources
including the Behavioral Risk Factor Surveillance System (BRFSS) and
Statewide Planning and Research Cooperative System (SPARCS) databases.
For each time period, data was loaded from a CSV file, skipping the
first two rows, and variable names were standardized using the janitor
package’s clean_names() function to ensure consistent formatting.

The data was then filtered to include two specific health indicators:
asthma emergency department visit rates and asthma hospitalization rates
for ages 0-17 years. The geographic scope was limited to five counties
(New York, Queens, Richmond, Kings, and Bronx), and only relevant
variables were retained. These variables included data_years (the
specified three-year period of data collection), county (geographic
location), zip_code (postal code), indicator (type of healthcare
utilization), and zip_code_rate (rate per 10,000 children aged 0-17
years).

Variables were renamed: “Asthma emergency department visit rate per
10,000 - aged 0-17 years” to “ed_visits_rate” and “Asthma
hospitalization rate per 10,000 - aged 0-17 years” to “hosp_rate”.
County was renamed to borough to reflect NYC terminology. This involved
changing “New York” to “Manhattan”, “Kings” to “Brooklyn”, and
“Richmond” to “Staten Island”.

Missing and unstable values in zip_code_rate were handled in two ways:
values marked with “s” were converted to NA (indicating data suppressed
for confidentiality reasons due to not meeting reporting criteria), and
values marked with an asterisk were converted to NA (indicating fewer
than 10 events in the numerator, making the rate unstable).

The resulting datasets provide clean, consistently formatted data for
analyzing pediatric asthma-related healthcare utilization across New
York City boroughs at the ZIP code level for 2016-2018 and 2019-2021.
