# BUSINESS SCIENCE UNIVERSITY
# DS4B 203-R: ADVANCED TIME SERIES FORECASTING FOR BUSINESS
# MODULE: TIME SERIES JUMPSTART 

# GOAL: Forecast Daily Email Users - Next 8-WEEKS

# OBJECTIVES ----
# - Dive into a time-series analysis project
# - Experience Frameworks: modeltime
# - Experience 2 Algorithms:
#   1. Prophet
#   2. LM w/ Engineered Features

# LIBRARIES ----

if (!require("pacman")) install.packages("pacman")
pacman::p_load("tidyverse", "tidymodels", "modeltime",
               "DataExplorer", "timetk", "lubridate")
set.seed(8675309)

# DATA -----

mailchimp_users_tbl <- read_rds("./00_data/mailchimp_users.rds")

# 1.0 EDA & DATA PREP ----

# * DAILY SUBSCRIBERS INCREASES

mailchimp_users_tbl %>% glimpse()

# * Count of optins by day ----

mailchimp_users_tbl %>% 
  summarise_by_time(
    .date_var = optin_time,
    .by = "day",
    optins = n()
  ) -> optins_day_tbl

# * Summary Disgnostics ----

optins_day_tbl %>% 
  tk_summary_diagnostics(
    .date_var = optin_time
  )

# * Pad the time series ----

optins_day_tbl %>% 
  pad_by_time(
    .date_var = optin_time,
    .by = "day",
    .pad_value = 0
  ) -> optins_day_prep_tbl

# * Visualization ----

optins_day_prep_tbl %>% 
  plot_time_series(
    .date_var = optin_time,
    .value = optins
  )

# 2.0 EVALUATION PERIOD ----

# * Filtering ----

optins_day_prep_tbl %>% 
  filter_by_time(
    .date_var = optin_time,
    .start_date = "2018-11-20",
    .end_date = "end"
  ) -> eval_tbl

eval_tbl %>% 
  plot_time_series(
    .date_var = optin_time,
    .value = optins
  )

# * Training / Testing ----

eval_tbl %>% 
  time_series_split(
    date_var = optin_time,
    assess = "8 weeks",
    cumulative = T
  ) -> splits

splits

splits %>% 
  tk_time_series_cv_plan() %>% 
  plot_time_series_cv_plan(optin_time, optins)

# 3.0 PROPHET FORECASTING ----

# * Prophet Model using modeltime/parsnip ----

prophet_reg() %>% 
  set_engine("prophet") %>% 
  fit(optins ~ ., data = training(splits)) -> mod_prophet_fit

# * Modeltime process ----

modeltime_table(
  mod_prophet_fit
) -> model_tbl

# * Calibration ----

model_tbl %>% 
  modeltime_calibrate(
    new_data = testing(splits)
  ) -> calibration_tbl

# * Visualize Forecast ----

calibration_tbl %>% 
  modeltime_forecast(
    actual_data = eval_tbl
  ) %>% 
  plot_modeltime_forecast()

# * Get Accuracy Metrics ----

calibration_tbl %>% 
  modeltime_accuracy()

# 4.0 FORECASTING WITH FEATURE ENGINEERING ----

# * Identifying Possible Features ----

eval_tbl %>% 
  plot_seasonal_diagnostics(
    .date_var = optin_time,
    .value = log(optins)
  )

# * Recipe Spec ----

recipe(optins ~ ., training(splits)) %>% 
  step_timeseries_signature(optin_time) %>% 
  step_rm(ends_with(".iso"), ends_with(".xts"),
          contains("hour"), contains("minute"),
          contains("second"), contains("am.pm")) %>%
  step_normalize(ends_with("index.num"),
                 ends_with("_year")) %>% 
  step_dummy(all_nominal()) -> recipe_spec

recipe_spec %>% prep() %>% juice() %>% glimpse()

# * Machine Learning Specs ----

linear_reg() %>% 
  set_engine("lm") -> mod_lm_spec

workflow() %>% 
  add_model(mod_lm_spec) %>% 
  add_recipe(recipe_spec) %>% 
  fit(training(splits)) -> wflw_lm_fit

# * modeltime process ----

modeltime_table(
  mod_prophet_fit,
  wflw_lm_fit
) %>% 
  modeltime_calibrate(testing(splits)) -> calibration_tbl

calibration_tbl %>% 
  modeltime_accuracy()

calibration_tbl %>% 
  modeltime_forecast(
    new_data = testing(splits),
    actual_data = eval_tbl
  ) %>% 
  plot_modeltime_forecast()

# 5.0 SUMMARY & NEXT STEPS ----

# * What you've learned ----
# - You've been exposed to:
#   - Tidymodels / Modeltime Framework
# - You've seen 2 modeling approaches:
#   - Prophet - Univariate, Automatic
#   - Linear Regression Model - Many recipe steps
# - You've experienced Feature Engineering
#   - Visualizations: ACF, Seasonality
#   - Feature Engineering from Date Variables
#
# * Where you are going! ----
# - You still need to learn:
#   - New algorithms
#   - Machine Learning - How to tune parameters
#   - Feature Engineering Strategies
#   - Ensembling - Competition winning strategy
#   - and a lot more!

