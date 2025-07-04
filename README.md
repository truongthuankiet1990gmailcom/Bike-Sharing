# Cyclistic Bike Share Case Study
## 📝 Introduction
The Cyclistic Bike Share Case Study is a capstone project for the Google Data Analytics Professional Certificate on Coursera. In this project, I follow the data analysis process: ask, prepare, process, analyze, share and act to analyze bike-sharing data and provide actionable insights.

## 💬 Background
- Cyclistic is a bike-share company based in Chicago that launched a successful bike-sharing program in 2016. Throughout the years, the program has expanded significantly to a fleet of 5,824 bicycles and a network of 692 geotracked stations across the city. With bicycles distributed across numerous stations, customers can rent bikes from one station and return them to any other station within the network at their convenience.

- Cyclistic's marketing strategy has focused on building general awareness and appealing to broad consumer segments. The company offers flexible pricing plans including single-ride passes, full-day passes, and annual memberships. The company also provides reclining bikes, hand tricycles, and cargo bikes, making the service accessible to people with disabilities.

- The company's marketing director believes that maximizing the number of annual memberships is key to future success. Therefore, my task is to understand how casual riders and annual members use Cyclistic bikes differently to design an effective marketing strategy to convert casual riders into annual members.

## ⚙ Approach/Steps
### **Ask**
- Business Task: Design marketing strategies to convert casual riders to members by understanding how annual and casual riders differ, why casual riders would buy a membership, and how digital media could affect marketing tactics.

- Questions guiding this analysis:

    - How do annual members and casual riders use Cyclistic bikes differently?
    - Why would casual riders buy Cyclistic annual memberships?
    - How can Cyclistic use digital media to influence casual riders to become members?
### **Prepare**
- 🔗 Quick Links
Data Source: [divvy-tripdata]((https://divvy-tripdata.s3.amazonaws.com/index.html))<br>
[Note that the data has been made available by Motivate International Inc. under this license.]

- Tools Used:

    - Data combining - Python with pandas
    - Data cleaning & processing - R with tidyverse, lubridate, and hms packages
    - Data visualization - Tableau
### **Process**
- The analysis is based on 2024 data, processed through the following steps:

- Data Combining


I used Python to combine 12 monthly CSV files into a single dataset:
```python
# Code in combiner.ipynb
import pandas as pd
import numpy as np

def merge_dataframes(file_list, date_column='started_at'):
    dataframes = []
    for file in file_list:
        try:
            df = pd.read_csv(file, parse_dates=[date_column])
            dataframes.append(df)
            print(f"Successfully loaded {file}")
        except FileNotFoundError:
            print(f"Warning: File {file} not found and will be skipped")
        except ValueError as e:
            print(f"Warning: {e}. Loading {file} without parsing dates")
            df = pd.read_csv(file)
            dataframes.append(df)
    
    if dataframes:
        merged_df = pd.concat(dataframes, ignore_index=True)
        return merged_df
    else:
        raise ValueError("No valid files were found to merge")

# Files to combine (all 12 months of 2024)
file_lists = ['202401-divvy-tripdata.csv', '202402-divvy-tripdata.csv', 
              '202403-divvy-tripdata.csv', '202404-divvy-tripdata.csv',
              '202405-divvy-tripdata.csv', '202406-divvy-tripdata.csv',
              '202407-divvy-tripdata.csv', '202408-divvy-tripdata.csv',
              '202409-divvy-tripdata.csv', '202410-divvy-tripdata.csv',
              '202411-divvy-tripdata.csv', '202412-divvy-tripdata.csv']

# Combine the files
merged_df = merge_dataframes(file_lists)

# Export the combined data
merged_df.to_csv('merged_divvy_tripdata_2024.csv', index=False)
```

Data Cleaning and Transformation

Using R, I cleaned and transformed the data:
```R
# Code from preprocessing.R
library(tidyverse)
library(lubridate)
library(hms)
library(data.table)

# Load the combined dataset
cyclistic_df <- read_csv("merged_divvy_tripdata_2024.csv")
cyclistic_date <- cyclistic_df

# Calculate ride duration
cyclistic_date$ride_length <- difftime(cyclistic_df$ended_at, cyclistic_df$started_at, units = "mins")

# Create datetime-related columns
cyclistic_date$date <- as.Date(cyclistic_date$started_at)
cyclistic_date$day_of_week <- format(as.Date(cyclistic_date$date), "%A")
cyclistic_date$month <- format(as.Date(cyclistic_date$date), "%m")
cyclistic_date$day <- format(as.Date(cyclistic_date$date), "%d")
cyclistic_date$year <- format(as.Date(cyclistic_date$date), "%Y")
cyclistic_date$time <- as_hms((cyclistic_df$started_at))
cyclistic_date$hour <- hour(cyclistic_date$time)

# Clean the data
cyclistic_date <- na.omit(cyclistic_date)
cyclistic_date <- distinct(cyclistic_date)
cyclistic_date <- cyclistic_date[!(cyclistic_date$ride_length <=0),]
```

### **Analyze**
The analysis focused on comparing usage patterns between members and casual riders:

**Key Metrics Analyzed:**
- Total number of rides by membership type
- Bike type preferences
- Average ride duration
- Weekly usage patterns
- Monthly/seasonal trends
- Time of day preferences

#### How do annual members and casual riders use Cyclistic bikes differently?
<img src = "Images/Q1_1.png">
<img src = "Images/Q1_2.png">

**Average Ride Length**
- Significant difference in ride duration: Casual riders average 24.05 minutes per ride compared to members at 12.47 minutes
- This suggests casual riders use bikes for nearly twice as long per trip
- The shorter rides by members might indicate utilitarian usage (commuting, errands) versus leisure rides for casual users

**Total Ridership**
- Members dominate overall usage: 63.84% of all rides (2,686,559) are taken by members compared to 36.16% (1,521,537) by casual riders
- Total system usage is substantial at 4.2M rides
- Despite being fewer in number, members are generating nearly 2/3 of all system activity

**Bike Type Preferences**
- Classic bikes are preferred across both user types:
    - Members: 1,759,216 classic bike rides vs. 905,256 electric bike rides
    - Casual riders: 968,853 classic bike rides vs. 526,944 electric bike rides
- Both groups show similar proportional preference for classic bikes over electric options
- Electric scooters represent a very small portion of the total rides (25,740)
#### Why would casual riders buy Cyclistic annual memberships?

- **Financial incentives:** Cost savings for those who take longer rides (24.05 min average) or ride frequently, especially weekend users who would reach the break-even point quickly

- **Enhanced convenience:** Streamlined access without per-ride payments, allowing spontaneous rides without time limits or overage fees

- **Usage expansion:** Encouragement to incorporate weekday rides beyond current weekend-focused patterns, transforming recreational riders into regular transportation users
<img src = "Images/Q2_1.png">

- **Seasonal value:** Better economics for warm-weather riders (May-September) compared to pay-per-ride options during peak months
<img src = "Images/Q2_2.png">

- **Community benefits:** Sense of belonging with access to potential member-exclusive features and priority service during high-demand periods

#### How can Cyclistic use digital media to influence casual riders to become members?
**Targeted Weekend Campaigns**

- Create social media ads specifically targeting weekend leisure riders
- Highlight membership benefits during peak weekend hours
- Use location-based notifications near popular recreational routes

**Seasonal Digital Promotions**

- Launch campaigns before the peak May-September season
- Offer early-bird membership discounts through digital channels
- Create urgency with limited-time offers at season start

**Data-Driven Personalization**

- Send personalized savings calculations to casual riders based on their usage
- Develop interactive cost-comparison tools showing potential savings
- Use app notifications highlighting how close users are to "breaking even" with a membership

**Compelling Content Marketing**

- Share success stories of converted members
- Create visual content showcasing membership benefits during leisure rides
- Develop rider community content highlighting weekend experiences
- Digital Ride Experience Enhancement

**Promote member-exclusive digital features**

- Highlight the seamless, payment-free experience for impromptu rides
- Showcase special routes or experiences available to members

### **Share**

View dashboard: https://public.tableau.com/app/profile/truong.kiet2362/viz/BikeSharing_17486830939870/Dashboard1

The analysis revealed several important patterns:

- **Similarities:**

    - Both members and casual riders prefer riding in warmer months (May-September)
    - Both groups prefer classic bicycles over electric bicycles
    Both show higher average ride durations on weekends compared to weekdays
- **Differences:**

    - Members take more frequent but shorter rides
    - Members show consistent usage throughout the week, while casual riders peak on weekends
    - Casual riders have significantly longer average ride durations
    - Members likely use bikes for commuting/practical purposes, while casual riders use them more for leisure

### **Act**
**Recommendations**
- Flexible Membership Options: Create weekend-focused and seasonal membership plans to appeal to casual riders who primarily use bikes on weekends or during specific seasons.

- Value-Based Marketing: Highlight cost savings of membership versus pay-per-ride for casual riders who take longer rides.

- Loyalty Program: Implement a points system where longer rides earn more points toward membership discounts.

- Weekend Experience Enhancement: Offer special member benefits during weekends to convert weekend leisure riders.

- Digital Media Strategy: Use targeted social media campaigns showcasing the joy of cycling in different scenarios, with emphasis on weekend recreational benefits of membership.

Community Building: Organize member-exclusive events and group rides to create a sense of belonging.

## Conclusion
The analysis provides clear insights into how casual riders and members use Cyclistic bikes differently. By creating targeted marketing strategies that address the specific usage patterns and preferences of casual riders, Cyclistic can effectively increase membership conversions and build a more sustainable business model.