# Employment Status Data Transformation Project

## Overview
This project demonstrates the transformation of employment statistics data from wide format to tidy (long) format, following tidy data principles. The dataset contains employment statistics comparing people with and without disabilities across two time periods (August 2024 and August 2025).

## Project Goals
- Transform untidy (wide) data into tidy (long) format
- Demonstrate best practices for data cleaning and reshaping
- Create a dataset ready for analysis and visualization
- Follow tidy data principles where each variable forms a column and each observation forms a row

## Dataset
**Source:** Employment status statistics by disability status  
**Original Format:** Wide format with disability status and dates as column headers  
**Time Period:** August 2024 to August 2025  
**Categories:** Labor force participation, employment, unemployment rates, and demographic breakdowns by age and sex

### Original Structure (Wide Format)
- Multiple variables in column headers (disability status + date)
- Column headers contain data values rather than variable names
- Difficult to filter, group, and analyze

### Transformed Structure (Tidy Format)
Five clean variables:
- **Category:** Employment metric (e.g., "Employed", "Participation rate")
- **Disability_Status:** "With Disability" or "No Disability"
- **Year:** "2024" or "2025"
- **Month:** "Aug"
- **Value:** Numeric value for the metric

## Files
- `employment_stats.csv` - Original wide format data
- `transforming_data.Rmd` - R Markdown file with transformation code
- `employment_status_tidy.csv` - Output tidy format data
- `README.md` - This file

## Requirements
```r
library(tidyverse)  # For data manipulation and transformation
library(readr)      # For reading CSV files
```

## Transformation Process

### 1. Data Import and Exploration
- Read CSV file (skip first header row)
- Examine structure and data types
- Identify unique categories and check for empty rows

### 2. Data Cleaning
- Rename columns to descriptive names
- Remove empty rows
- Verify data integrity

### 3. Transformation to Long Format
- Use `pivot_longer()` to convert from wide to long
- Separate combined variables (disability status and date)
- Extract year and month components
- Convert values to appropriate numeric format

### 4. Validation
- Check final structure
- Verify all unique categories are preserved
- Ensure data types are correct

### 5. Export
- Save tidy dataset as CSV for future use

## Key Transformations

```r
# Convert from wide to long
employment_long <- employment_wide %>%
  pivot_longer(
    cols = -Category,
    names_to = "Group_Year",
    values_to = "Value"
  )

# Separate and clean
employment_tidy <- employment_long %>%
  separate(Group_Year, into = c("Disability_Status", "Month_Year"), sep = "_") %>%
  mutate(
    Disability_Status = case_when(
      Disability_Status == "Disability" ~ "With Disability",
      Disability_Status == "NoDisability" ~ "No Disability"
    ),
    Year = str_extract(Month_Year, "\\d{4}"),
    Month = str_remove(str_extract(Month_Year, "[A-Za-z]+\\.?"), "\\.")
  )
```

## Benefits of Tidy Data
- **Easier filtering:** Simple `filter()` operations by any variable
- **Simpler grouping:** Use `group_by()` on any combination of variables
- **Direct plotting:** Works seamlessly with ggplot2
- **Scalable analysis:** Operations apply automatically to all groups
- **Readable code:** Clear, concise data manipulation

## Usage

### Running the Analysis
1. Place `employment_stats.csv` in your working directory
2. Open `transforming_data.Rmd` in RStudio
3. Click "Knit" to generate HTML output
4. The tidy dataset will be saved as `employment_status_tidy.csv`

### Using the Tidy Dataset
```r
# Load the tidy data
employment_tidy <- read_csv("employment_status_tidy.csv")

# Example: Filter 2025 data for people with disabilities
employment_tidy %>%
  filter(Year == "2025", Disability_Status == "With Disability")

# Example: Calculate participation rate changes
employment_tidy %>%
  filter(Category == "Participation rate") %>%
  group_by(Disability_Status) %>%
  arrange(Year) %>%
  mutate(Change = Value - lag(Value))
```

## Key Findings
- Significant employment gap between people with and without disabilities
- Participation rate: 25.1% (with disability) vs 67.9% (no disability) in 2025
- Employment growth from 2024-2025: 9.4% for people with disabilities, 0.8% for people without disabilities

## Author
Candace Grant

## Date
October 5, 2025

## License
This project is for educational purposes.
