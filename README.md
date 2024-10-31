# Economic Dashboard of Chicago Neighborhoods

This project provides an interactive dashboard to explore the economic data of Chicago, visualizing average personal income and other key socioeconomic indicators at the neighborhood level.

You can access the live dashboard [here](https://zesty-moonbeam-84397f.netlify.app/).

## Overview

This lightweight dashboard is designed to help users understand Chicago's neighborhoods through data on various socioeconomic indicators. Using cross-filtering and interactive tooltips, the dashboard offers an engaging way to look into average income, poverty rates, unemployment status, and the correlations between undereducation and income levels across different regions.

## Features

- **Cross-Filtering**: The dashboard allows you to interactively select data by brushing over specific areas on the plots. This functionality enables a focus on data points that interest you and explore their relationships with other variables.
- **Interactive Tooltips**: The map on the left displays nearly one hundred neighborhoods in Chicago, color-coded by income level. Hovering over each region shows you its neighborhood name and average personal income, providing valuable context.
- **Data Visualization**: The right side of the dashboard features three plots; two histograms and one scatterplot. The histograms address households under the poverty line and the unemployed population, while the scatterplot shows the relationship between undereducation rates and income levels.
  
## Data

The data used in this dashboard includes:

- **Neighborhood Name**: Primary neighborhoods in the Chicago area.
- **Income**: Average income per capita for each neighborhood.
- **Poverty Rate**: Percentage of households below the poverty line.
- **Unemployment**: Percentage of individuals aged 16 and older who are unemployed.
- **Undereducation**: Percentage of individuals aged 25 and older without a high school diploma.

## Source

The datasets are sourced from the Chicago Data Portal. The [economic dataset](https://data.cityofchicago.org/Health-Human-Services/Per-Capita-Income/r6ad-wvtk) is based on per capita income data from census records between 2008 and 2012. Geographic coordinates are obtained from the [Chicago neighborhood boundaries dataset](https://data.cityofchicago.org/Facilities-Geographic-Boundaries/Boundaries-Neighborhoods/bbvz-uum9) available on the same portal.

## Note

The legend displays nine income ranges, with each number indicating the lowest value within each bin, and it is continuously updated during cross-filtering.