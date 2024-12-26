# MTA Ridership Analysis Project

This project analyzes the MTA ridership data to understand the impact of the COVID-19 pandemic on public transportation services and track recovery trends over time. It includes calculating pre-pandemic traffic shares, comparing ridership across services, identifying the biggest drops, and visualizing recovery patterns.

### Data Preparation and Transformation

#### Step 1: Import the Dataset
- The dataset was imported into Power BI using the Text/CSV data connector.
- Column data types were verified to ensure the following:
1. Date column was set to Date format.
2. Numeric columns for ridership and percentages were set to Whole Number or Decimal formats.

#### Step 2: Unpivoting Columns
To make the dataset suitable for analysis, two separate transformations were performed for ridership totals and percentage recovery values.

##### Part 1: Unpivot Ridership Data
- Columns Unpivoted: All columns containing total ridership values (e.g., Subways: Total Estimated Ridership, Buses: Total Estimated Ridership, etc.).
- After unpivoting:
1. The original column names became values in a new column named Attribute.
2. The ridership totals were stored in a new column named Value.
- Service Name Extraction:
1. The Attribute column was split into two parts using : as the delimiter:
2. The first part (e.g., Subways, Buses) was extracted and renamed to Transportation.
3. The second part was discarded.

##### Part 2: Unpivot Percentage Data
- Columns Unpivoted: All columns containing percentage values (e.g., Subways: % of Comparable Pre-Pandemic Day, etc.).
- Similar steps were performed:
1. Column names were unpivoted into an Attribute column, which was split to extract the service name.
2. The percentage values were stored in a new column named %.

#### Step 3: Merging Ridership and Percentage Data
- The unpivoted ridership and percentage tables were merged using a Left Join based on the Date and Transportation columns.
- After merging:
Each row contained Date, Transportation, Passengers After Covid (ridership totals), and % vs Pre-Pandemic.

### Step 3: Calculated Columns & Measures

#### Calculated Columns
1. Pre-Pandemic Passengers:
```
Blanks_PrePandemicPassengers = 
    IF(
        'Total Estimated Ridership'[Percentage] > 0,
        'Total Estimated Ridership'[Total_Ridership] / ('Total Estimated Ridership'[Percentage] / 100),
        BLANK()
    )
```
 
Purpose: Estimate the ridership before the pandemic for each service and date.

2. Day Name:

```
Day Name = FORMAT('Total Estimated Ridership'[Date], "dddd")
```

Extract the day name from the Date column (e.g., Monday, Saturday).
Purpose: Identify weekdays and weekends.

3. Week/Weekend:

```
Week/Weekend = 
IF (
    'Total Estimated Ridership'[Day Name] = "Saturday" || 'Total Estimated Ridership'[Day Name] = "Sunday", 
    "Weekend", 
    "Weekday"
)
```

Weekends: If Day Name is Saturday or Sunday.
Weekdays: For all other days.
Purpose: Categorize days into weekdays or weekends for filtering and comparison.

4. Month Name:


Extract the full name of the month from the Date column (e.g., January).
Purpose: Group data by month for analysis.

5. Year:

```
Year = YEAR([Date])
```

Extract the year from the Date column (e.g., 2020).
Purpose: Support year-based grouping.

6. Month and Year:

```
Month and Year = 
FORMAT('Total Estimated Ridership'[Date], "MMMM, yyyy")
```
Combine Month Name and Year into a single string (e.g., January, 2020).
Purpose: Provide a user-friendly time grouping for visualizations.

7. Sort Month and Year:

```
Sort Month and Year = 
YEAR('Total Estimated Ridership'[Date]) * 100 + MONTH('Total Estimated Ridership'[Date])
```

Formula: Assign a numeric index to Month and Year to ensure proper chronological sorting.
Purpose: Maintain the correct order of months and years in visuals.

8. % Change vs. Pre-Pandemic:

```
% vs Pre-Pandemic = ('Total Estimated Ridership'[Total_Ridership] / 'Total Estimated Ridership'[PrePandemicPassengers]) - 1
```

Purpose: Determine the percentage change in ridership compared to pre-pandemic levels.

#### Calculated Measures

1. Pre-Pandemic Ridership:

```
Pre-Pandemic Ridership = 
SUM('Total Estimated Ridership'[Blanks_PrePandemicPassengers])
```

Purpose: Calculate the total estimated ridership before the pandemic across all services.

2. Post-Pandemic Ridership:

```
Post-Pandemic Ridership = 
SUM('Total Estimated Ridership'[Total_Ridership])
```

Purpose: Calculate the total post-pandemic ridership across all services.

### Custom Tooltip Measures

1. Tooltip Title:



Purpose: Display the selected month, year, and whether the data point represents weekends or weekdays.

2. Tooltip Main Content:

```
 VAR service = 
IF(
    ISFILTERED('Total Estimated Ridership'[Service]),
    IF(
        DISTINCTCOUNT('Total Estimated Ridership'[Service]) = 1,
        SELECTEDVALUE('Total Estimated Ridership'[Service]),
        IF(
            DISTINCTCOUNT('Total Estimated Ridership'[Service]) = 7,
            "All Services",
            CONCATENATEX(
                VALUES('Total Estimated Ridership'[Service]),
                'Total Estimated Ridership'[Service],
                ", ",
                'Total Estimated Ridership'[Service],
                ASC
            )
        )
    ),
    "All Services"
)
```

Purpose: Provide detailed ridership metrics in the tooltip for each data point.

### Step 5: Create Visualizations in Power BI

1. Scatter Chart
This chart visualizes the relationship between post-pandemic ridership, percentage recovery, and the trend over time for each service.

Steps to Build the Scatter Chart
- Add a Scatter Chart to the Canvas:
- Configure the Scatter Chart:
1. X-Axis: Passengers After Covid (post-pandemic ridership).
2. Y-Axis: % Change vs. Pre-Pandemic.
3. Size: Post-Pandemic Ridership (to represent the total ridership as bubble size).
4. Legend: Transportation (to distinguish services by color).
5. Play Axis: Month and Year (for temporal animation).
Ensure it is sorted using the Sort Month and Year column for correct chronological order.
- Add Tooltips: Add your custom tooltip measures (Tooltip Title and Tooltip Main) to the Tooltips field.

![Pre_Post Pandemic Data](https://github.com/user-attachments/assets/6a7da942-ea98-40fe-8fe9-8e00c037b9e5)

2. Area Chart
This chart provides a time-series comparison of pre- and post-pandemic ridership for each service.

Steps to Build the Area Chart
- Add an Area Chart to the Canvas:
- Configure the Area Chart:
1. X-Axis: Month and Year (sorted using Sort Month and Year).
2. Y-Axis: Add two lines:
3. Pre-Pandemic Ridership.
4. Post-Pandemic Ridership.
5. Legend: Add a legend to differentiate the two lines (e.g., "Pre-Pandemic" and "Post-Pandemic").
6. Add Filters and Slicers:
a. Transportation Slicer
b. Week/Weekend Slicer

![Total_Ridership](https://github.com/user-attachments/assets/cecd386b-248a-4b00-9528-f398c164eb5a)

3. TootTip

![Toottip](https://github.com/user-attachments/assets/74621795-bcac-4185-bd17-f8422cc98f2b)

### Step 6: Insights and Sharing

1. What was the share of traffic by service before the pandemic? Did it change?

2. Which service saw the biggest drop-off in traffic? Has it recovered?

3. Which service was the fastest to recover?

4. When are all the services estimated to return to pre-pandemic levels?
