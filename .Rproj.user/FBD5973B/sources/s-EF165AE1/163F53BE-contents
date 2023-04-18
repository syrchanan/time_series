# BUSINESS SCIENCE UNIVERSITY
# DS4B 203-R: ADVANCED TIME SERIES FORECASTING FOR BUSINESS
# MODULE: INSPECT COURSE DATASETS


# LIBRARIES ----
library(tidyverse)


# DATA -----

# * Establish Relationships ----
#   - Website traffic (Page Views, Sessions, Organic Traffic)
#   - Top 20 Pages

google_hourly_summary <- read_rds("./00_data/google_analytics_summary_hourly.rds")

google_daily_page <- read_rds("./00_data/google_analytics_by_page_daily.rds")

# * Build Relationships ----
#   - Collect emails
#   - Host Events

mailchimp_users <- read_rds("./00_data/mailchimp_users.rds")

learning_labs <- read_rds("./00_data/learning_labs.rds")

# * Generate Course Revenue ----
#   - Revenue data (aggregated at weekly interval)
#   - Product Events

weekly_transactions <- read_rds("./00_data/transactions_weekly.rds")

product_events <- read_rds("./00_data/product_events.rds")