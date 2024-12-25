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

#### Calculated Columns
1. Pre-Pandemic Passengers:
```
PrePandemicPassengers = 
    'Total Estimated Ridership'[Total_Ridership] / 
    ('Total Estimated Ridership'[Percentage]/100)
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
