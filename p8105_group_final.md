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

ed_2016_2018 <- read_csv("data/ed_data_csv.csv", skip = 2) %>%
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

ed_2019_2021 <- read_csv("data/ed_data_csv.csv", skip = 2) %>%
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

##### Temperature

<https://open-meteo.com/en/docs/historical-weather-api>

used this website and recorded the coordinates of 5 boroughs and
extracted the temperatrue information by daily in a timeframe between
2016 and 2021.

``` r
manhattan = read_csv("data/manhattan.csv",skip = 2) |>
  mutate(indicator = "manhattan") |> 
  janitor::clean_names()
```

    ## Rows: 2192 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl  (3): temperature_2m_max (°C), temperature_2m_min (°C), temperature_2m_m...
    ## date (1): time
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
bronx = read_csv("data/bronx.csv",skip = 2) |>
  mutate(indicator = "bronx") |> 
  janitor::clean_names()
```

    ## Rows: 2192 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl  (3): temperature_2m_max (°C), temperature_2m_min (°C), temperature_2m_m...
    ## date (1): time
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
brooklyn = read_csv("data/brooklyn.csv",skip = 2) |>
  mutate(indicator = "brooklyn") |> 
  janitor::clean_names()
```

    ## Rows: 2192 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl  (3): temperature_2m_max (°C), temperature_2m_min (°C), temperature_2m_m...
    ## date (1): time
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
queens = read_csv("data/queens.csv",skip = 2) |>
  mutate(indicator = "queens") |> 
  janitor::clean_names()
```

    ## Rows: 2192 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl  (3): temperature_2m_max (°C), temperature_2m_min (°C), temperature_2m_m...
    ## date (1): time
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
staten_island = read_csv("data/staten_island.csv",skip = 2) |>
  mutate(indicator = "staten_island") |> 
  janitor::clean_names()
```

    ## Rows: 2192 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl  (3): temperature_2m_max (°C), temperature_2m_min (°C), temperature_2m_m...
    ## date (1): time
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

I then merged the data by borough and divided into two groups to
compare.

``` r
all_temp = bind_rows(manhattan, bronx, brooklyn, queens, staten_island) |> 
  mutate(indicator = as.factor(indicator)) |>
  mutate(date = as.Date(time, format = "%Y-%m-%d")) |> 
  mutate(date_group = ifelse(year(date) %in% 2016:2018, "2016-2018", "2019-2021")) |> 
  select(temperature_2m_max_c:date_group) |> 
  group_by(date_group, indicator) |>
  summarise(avg_temperature = mean(temperature_2m_mean_c, na.rm = TRUE))
```

    ## `summarise()` has grouped output by 'date_group'. You can override using the
    ## `.groups` argument.

``` r
write.csv(all_temp, "data/NYC_temp.csv", row.names = FALSE)

all_temp_raw = bind_rows(manhattan, bronx, brooklyn, queens, staten_island) |> 
  mutate(indicator = as.factor(indicator))

write.csv(all_temp_raw, "data/NYC_temp_raw.csv", row.names = FALSE)

ggplot(data = all_temp, aes(x = date_group, y = avg_temperature, color = indicator)) +
  geom_point(size = 3) +
  geom_line(aes(group = indicator), size = 1) +
  labs(title = "Average Temperature by Date Group and Indicator",
       x = "Date Group",
       y = "Average Temperature") +
  theme_minimal()
```

    ## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `linewidth` instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

![](p8105_group_final_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## Greenspace Data

Greenspace data:
<https://data.cityofnewyork.us/Recreation/Parks-Properties/enfh-gkve/about_data>

``` r
greenspace_clean = greenspace_df |>
  mutate(
    borough = case_match(borough, 
      "R" ~ "Staten Island",
      "Q" ~ "Queens",
      "X" ~ "Bronx",
      "B" ~ "Brooklyn",
      "M" ~ "Manhattan"),
    typecategory = as.factor(typecategory),
    borough = as.factor(borough)) |>
  separate(acquisitiondate, into = c("year", "month", "day"), sep = "-") |>
  select(year, borough, acres) |> 
  filter(year < 2022 | is.na(year))
   
#keep date NA; up to 2019

summary(greenspace_clean)
```

    ##      year                    borough        acres         
    ##  Length:2038        Bronx        :396   Min.   :   0.001  
    ##  Class :character   Brooklyn     :618   1st Qu.:   0.140  
    ##  Mode  :character   Manhattan    :391   Median :   0.713  
    ##                     Queens       :473   Mean   :  14.950  
    ##                     Staten Island:160   3rd Qu.:   2.033  
    ##                                         Max.   :2771.747

#### Dataset 1: 2016-2018 (3 years)

``` r
dataset1_gs = greenspace_clean |>
  filter(year < 2019 | is.na(year)) |> 
  group_by(year, borough) |>
  summarise(avg_acres_per_yr_bor = mean(acres, na.rm = TRUE), .groups = "drop")

dataset1_gs_calc = dataset1_gs |> 
  group_by(borough) |> 
  summarise(acres_sum = sum(avg_acres_per_yr_bor, na.rm = TRUE), .groups = "drop") |> 
  mutate(year_group = "2016-2018")
```

#### Dataset 2: 2019-2021 (3 years)

``` r
dataset2_gs = greenspace_clean |>
  filter(year < 2022 | is.na(year)) |>
  group_by(year, borough) |>
  summarise(avg_acres_per_yr_bor = mean(acres, na.rm = TRUE), .groups = "drop")

dataset2_gs_calc = dataset2_gs |> 
  group_by(borough) |> 
  summarise(acres_sum = sum(avg_acres_per_yr_bor, na.rm = TRUE), .groups = "drop") |> 
  mutate(year_group = "2019-2021")
```

#### comparing by the year

``` r
total_acres = bind_rows(dataset1_gs_calc, dataset2_gs_calc) |>
  mutate(
    percentage = case_when(
      borough == "Manhattan" ~ (acres_sum/300800)*100,
      borough == "Bronx" ~ (acres_sum/571072)*100,
      borough == "Brooklyn" ~ (acres_sum/1093312)*100,
      borough == "Queens" ~ (acres_sum/1349120)*100,
      borough == "Staten Island" ~ (acres_sum/1127936)*100
    )
  ) |> 
  select(borough, percentage, year_group) |> 
  pivot_wider(names_from = year_group,
              values_from = percentage)

total_acres
```

    ## # A tibble: 5 × 3
    ##   borough       `2016-2018` `2019-2021`
    ##   <fct>               <dbl>       <dbl>
    ## 1 Bronx               0.198       0.219
    ## 2 Brooklyn            0.164       0.164
    ## 3 Manhattan           0.570       0.570
    ## 4 Queens              0.161       0.162
    ## 5 Staten Island       0.249       0.272

=======

### PM2.5

``` r
library(tidyverse)
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(readxl)
```

Import and clean PM2.5 data from website:
<https://www.kaggle.com/datasets/sahityasetu/new-york-city-air-quality?resource=download>.

``` r
particulate_matter = 
  read_csv("data/Air_Quality_20231208.csv")
```

    ## Rows: 16218 Columns: 12
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (7): Name, Measure, Measure Info, Geo Type Name, Geo Place Name, Time Pe...
    ## dbl (4): Unique ID, Indicator ID, Geo Join ID, Data Value
    ## lgl (1): Message
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

The raw data has 16218 rows and 12 columns.

The columns are Unique ID, Indicator ID, Name, Measure, Measure Info,
Geo Type Name, Geo Join ID, Geo Place Name, Time Period, Start_Date,
Data Value, and Message.

Unique ID: A unique identifier assigned to each row in the dataset.

Indicator ID: A code assigned to each indicator or measure of air
quality being tracked.

Name: The name or label given to the indicator or measure being tracked.

Measure: The unit of measurement used to quantify the air quality
indicator, such as parts per billion (ppb) for ozone or sulfur dioxide.

Measure Info: Additional information about the measurement or
calculation of the air quality indicator, if applicable.

Geo Type Name: The type of geographic area being tracked, such as
community districts (CDs) or boroughs.

Geo Join ID: A unique identifier assigned to each geographic area being
tracked.

Geo Place Name: The name of the specific geographic area being tracked,
such as Coney Island or the Bronx.

Time Period: The time period during which the air quality measurement
was taken, such as a specific season or winter of a particular year.

Start_Date: The date on which the air quality measurement period began.

Data Value: The value of the air quality indicator for the specific
geographic area and time period being tracked.

Message: Additional information or notes about the air quality
measurement or data value, if applicable.

As it stands, these data are not “tidy”: Unique ID should be a
character, as should Indicator ID and Geo Join ID.

We are interested in PM2.5 so we can filter name to “Fine particles (PM
2.5)”. I should note there is name option “Asthma emergency department
visits due to PM2.5”, “Respiratory hospitalizations due to PM2.5 (age
20+)”, “Cardiovascular hospitalizations due to PM2.5 (age 40+)”, “Deaths
due to PM2.5”, “Respiratory hospitalizations due to PM2.5 (age 20+)”,
corresponding to annual rates(18+, 30+, etc.) that could be interesting
to look at in secondary analyses.

Further filter to years of interest, we will be looking to compare
2016-2018 to 2019-2021.

We will make two datasets, one for analysis in which we will filter by
borough and create a new column that has the average PM2.5 across
2016-2018 and 2019-2021.

``` r
tidy_analysis_pm = 
  particulate_matter |>
  janitor::clean_names() |>
    mutate(
    unique_id = as.character(unique_id),
    indicator_id = as.character(indicator_id),
    geo_join_id = as.character(geo_join_id)) |>
  filter(name == "Fine particles (PM 2.5)") |>
  filter(time_period %in% c("Annual Average 2016", "Annual Average 2017",
                            "Annual Average 2018", "Annual Average 2019",
                            "Annual Average 2020", "Annual Average 2021")) |>
  filter(geo_type_name == "Borough") |>
  select(-unique_id, -indicator_id, -start_date) |>
    rename(borough = geo_place_name) |>
  mutate(
    period = case_when(
      time_period %in% c("Annual Average 2016", "Annual Average 2017", "Annual Average 2018") ~ "2016-2018",
      time_period %in% c("Annual Average 2019", "Annual Average 2020", "Annual Average 2021") ~ "2019-2021"
    )
  ) |>
  group_by(borough, period) |>
  summarize(
    average_pm2.5 = mean(data_value, na.rm = TRUE),
    .groups = "drop"
  ) |>
  pivot_wider(
    names_from = period,
    values_from = average_pm2.5,
    names_prefix = "avg_pm2.5_"
  )
```

Cleaned analysis PM2.5 dataset has 5 rows and 3 columns.

I made a new dataset that is by borough and individual years to be used
in the visualization step.

``` r
tidy_visualization_pm = 
  particulate_matter |>
  janitor::clean_names() |>
    mutate(
    unique_id = as.character(unique_id),
    indicator_id = as.character(indicator_id),
    geo_join_id = as.character(geo_join_id)) |>
  filter(name == "Fine particles (PM 2.5)") |>
  filter(time_period %in% c("Annual Average 2016", "Annual Average 2017",
                            "Annual Average 2018", "Annual Average 2019",
                            "Annual Average 2020", "Annual Average 2021")) |>
  filter(geo_type_name == "CD") |>
    rename(pm2.5_mcgm3 = data_value) 
```

``` r
geography = 
  read_excel("data/geoid_borough_name_nyc.xlsx") |>
  rename(geo_join_id = ID) |>
  mutate(geo_join_id = 
           as.character(geo_join_id))
```

``` r
pm_for_vis = 
    left_join(tidy_visualization_pm, geography, by = c("geo_join_id")) |>
    rename(neighborhood = Name) |>
    rename(borough = Borough) |>
   select(name, neighborhood, borough, time_period, pm2.5_mcgm3)
```

Cleaned visualization PM2.5 dataset has 354 rows and 5 columns.

This merged on the geo_join_id column for each community district. I
renamed some variables for clarity and selected the variables of
interest for visualization. \>\>\>\>\>\>\>
fa1dce32d0a33e55330f43be66c8e85822117900
