# Cyclistic Bike-Share Analysis Case Study

In this data analysis project, I take on the role of a data analyst for Cyclistic, a fictional bike-share company. To address key business questions, I apply the six-step data analysis process: **[Ask](#-01-ask), [Prepare](#%EF%B8%8F-02-prepare), [Process](#%EF%B8%8F-03-process), [Analyze](#-04-analyze-and--05-share), [Share](#-04-analyze-and--05-share),** and **[Act](#-06-act)**. This case study is from the [Google Data Analytics Capstone](https://www.coursera.org/learn/google-data-analytics-capstone).

---

## 🗂️ Project Overview

- **Tools Used**: Python, Pandas, Matplotlib, Folium, JupyterLab
- **Data Source**: [Divvy Trip Data](https://divvy-tripdata.s3.amazonaws.com/index.html)
- **Notebook**: [`cyclistic_analysis.ipynb`](cyclistic_analysis.ipynb)

---

## 💬 Scenario

I am a junior data analyst working on the marketing analyst team at Cyclistic, a bike-share company in Chicago. The director of marketing (Lily Moreno) believes the company’s future success depends on maximizing the number of annual memberships. Therefore, our team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, our team will design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve our recommendations, so they must be backed up with compelling data insights and professional data visualizations.

### Background

In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime. 

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members. 

Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Although the pricing flexibility helps Cyclistic attract more customers, Moreno believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a solid opportunity to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.

Moreno has set a clear goal: Design marketing strategies aimed at converting casual riders into annual members. In order to do that, however, the team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. Moreno and our team are interested in analyzing the Cyclistic historical bike trip data to identify trends.

---

## ❓ 01. Ask

### 🎯 Business Task

Analyze how casual riders and annual members use Cyclistic bikes differently to support a marketing strategy that converts casual riders into annual members.

**Guiding question**: How do I convert casual riders into annual members?

**Key stakeholders**: Lily Moreno (Director of marketing), Cyclistic executives, marketing analytics team, customers

---

## 🛠️ 02. Prepare

**Data Source**: [Divvy Trip Data](https://divvy-tripdata.s3.amazonaws.com/index.html)

**Time Period**: May 2024 - April 2025

**File Format**: 12 monthly ZIP files containing `.csv` ride data for a full year

### Data Description

The data source used for this analysis consists of public trip data provided by Motivate International Inc under this [license](https://divvybikes.com/data-license-agreement). The system is operated by Lyft Bikes and Scooters, LLC under contract with the City of Chicago, which owns the data. All data is anonymized and publicly accessible for non-commercial use. More information can be found [here](https://divvybikes-marketing-staging.lyft.net/system-data).

For this project, I access historical trip data spanning from **May 2024 to April 2025**. These datasets include details such as rider type (member or casual), bike type, trip start and end times, start and end stations, and station coordinates.

### Tools

I choose Python for this analysis due to its powerful data handling capabilities and extensive libraries for data cleaning and visualization, such as `pandas`, `matplotlib`, and `folium`. While this project can also be completed using SQL or spreadsheets, I opt to challenge myself by using Python to showcase my technical skills and gain experience working with a more versatile and scalable tool.

### Data Handling

I use Python’s built-in `zipfile` module to access and process the datasets directly in their zipped format, rather than manually unzipping and modifying each `.csv` file. This approach offers a more efficient and streamlined workflow, saving time and reducing the risk of data loss or inconsistencies that can occur during manual extraction and file handling.

---

## ⚙️ 03. Process

### Data Preview

Here is a preview of one of the datasets (`df_2405`):
![image](https://github.com/user-attachments/assets/63151a77-c73c-410a-b430-c4ae66b10b75)


### Data Cleaning

After storing the datasets into a dictionary, I clean and modify each dataframe to prep for analysis.
- Convert `started_at` and `ended_at` columns to datetime format
- Add `day_of_week` column (Sunday = 1 ... Saturday = 7)
- Check for missing values
- Check for duplicate rows

This is a snippet of the data validation output generated for one of the dataframes (`df_2405`):

![image](https://github.com/user-attachments/assets/712b6ada-64ef-4565-ae6c-c67caff77e37)

This tells me that for `df_2405`, there are missing values in these columns:
- `start_station_name`: 109048 values
- `start_station_id`: 109048 values
- `end_station_name`: 112731 values
- `end_station_id`: 112731 values
- `end_lat`: 784 values
- `end_lng`: 784 values

and that 27.45% of this dataset contain missing rows with 0 duplicates. 

After performing this process for all datasets, I can confirm that all contain values for the `started_at` and `ended_at` columns, which ensures that my calculated time-based columns (e.g., ride duration, day of week) are complete for every entry.

However, many missing values are found in the station name, station ID, and coordinates columns in all of the datasets. While I can still include station and geographic analysis, it’s important to acknowledge the limitations and potential inaccuracies this missing data introduces when evaluating station-level or location-based trends.

### Combining Data

I combine all the datasets into one dataframe (`combined_df`). Here are the columns and the datatypes:

![image](https://github.com/user-attachments/assets/6306f148-d2e2-4157-8aae-7260259ff782)

Summary statistics:

![image](https://github.com/user-attachments/assets/7b33aa51-e554-4907-b655-ead7d54cb41a)

The shortest recorded trip duration is around **-2 days**, which is not possible. This likely resulted from a data entry error or system malfunction. To ensure data accuracy, I remove this entry from my analysis and check for additional outliers.

![image](https://github.com/user-attachments/assets/a6ab5490-d47d-4500-b99f-4b898d96a76c)

When checking for outliers, I notice there are 126460 rides shorter than 60 seconds, so I decide to filter those out and create `filtered_df`.

As stated by Lyft:
    
*"The data has been processed to remove trips that are taken by staff as they service and inspect the system; and any trips that were below 60 seconds in length (potentially false starts or users trying to re-dock a bike to ensure it was secure)."*

However, some records with ride durations under 60 seconds are still present in the datasets, suggesting they may have been overlooked. To ensure data quality and consistency, I remove these trips, which results in a reduction of approximately **2.2%** of total rows.

Additionally, I remove the top 1% of rides (for a total of 3.2% removed) with unusually long durations to ensure the accuracy of my analysis, which include any rides longer than **99.80 minutes**. These extreme outliers may be the result of errors such as bikes not being properly docked or trips that were not correctly ended. Including them could skew my average ride length and other time-based metrics.

By filtering out these outliers, I retain 96.8% of the data, which gives me a more reliable representation of typical rider behavior. Now that my data is properly cleaned and processed, I can begin the analysis phase.

---

## 🔍 04. Analyze and 📊 05. Share

![image](https://github.com/user-attachments/assets/59366c37-254c-4a1a-861e-8d123566b819)

This summary provides insights into how ride lengths differ between casual riders and members:

- The majority of rides are taken by **members**
- **Casual riders** tend to take longer trips than **members** on average (~17.5 minutes vs ~11.5 minutes)
- **Members** show more consistent ride durations, indicated by a lower standard deviation and a tighter range between the 25th and 75th percentiles
- The maximum ride duration for both groups is now under 1 hour 40 minutes for both groups, which is much more realistic than before filtering

I can split the dataframe into two different sets for members (`member_df`) and casual riders (`casual_df`) for further exploration.

### Members vs. Casual Riders Trends

#### Month Counts

![image](https://github.com/user-attachments/assets/608dfc3e-32d4-48d2-8384-f3bf6949306a)

The monthly ride distribution follows a similar pattern for both groups, with peaks in the summer and fall and fewer rides during the winter months. This trend is likely a result of seasonal changes in weather and riding conditions.

It's important to note that members have a higher total ride count across all months because members make up more than 60% of the data. To meaningfully compare behaviors between groups, I need to use a normalized version of the chart that accounts for differences in group sizes and highlights relative trends.

![image](https://github.com/user-attachments/assets/108b4b6f-b0c9-462a-a908-5df4772f83fb)

In this normalized version, casual riders show a higher proportion of their annual rides occurring in the summer and fall compared to members. In contrast, members have a higher proportion during the winter. This suggests a more **need-basis for members, even during months with poor weather conditions**, whereas **casual riders are more likely to ride during favorable weather**.

#### Day Counts

Like with the months, I consider the normalized distribution of daily rides to better compare ride patterns.

![image](https://github.com/user-attachments/assets/dfd8c0c8-bb44-4c8f-af45-18e08cc653d9)

**Members** tend to ride more consistently throughout the workweek, peaking mid-week on Wednesdays. In contrast, **casual riders** show a strong preference for weekends, with Saturdays accounting for over 20% of their weekly rides. This pattern reinforces the idea that casual users likely ride for leisure, while members use the service more for commuting or routine travel.

#### Hour Counts

![image](https://github.com/user-attachments/assets/7d324372-ceab-4f92-b402-4542cf1244cd)

Both groups prefer to ride at similar hours. There is a peak in afternoon times around 5 PM for both members and casual riders. However, one distinction is that there is another peak in the morning around 8 AM as well for members, while the proportion of rides for casual riders is lower.

**Findings for Time-Based Data**

By analyzing the data by month, day of week, and hour of day, the data suggests that **members are more likely using the bikes for commuting purposes** whereas **casual riders exhibit patterns indicating leisure-based usage**.

#### Ride Types
![image](https://github.com/user-attachments/assets/4a3c521f-408a-4515-8126-4027f9a10846)
![image](https://github.com/user-attachments/assets/b30bbfce-34e1-467b-ac82-3387e37b1d63)

There are three rideable types:
- Electric bikes
- Classic bikes
- Electric scooters

**Electric bikes** are most popular amongst both groups, most likely for their speed and ease of use.

#### Popular Starting and Ending Stations
**Disclaimer:** The datasets are missing approximately 25-35% of data related to stations and coordinates. Therefore, the analysis may not fully represent all rides taken within the past year.

![image](https://github.com/user-attachments/assets/182eca38-ac43-4f0a-aeb1-d89eadb64b9d)
![image](https://github.com/user-attachments/assets/ab658393-57b6-4d91-8754-006c4b9ea651)

The top 5 starting and ending stations for each rider group reveal differing travel behaviors.
The most frequented stations for members and casual riders do not overlap, indicating that **each group tends to favor different parts of the city**.

**Map of Top 10 Start Stations for Each Rider Group**
![image](https://github.com/user-attachments/assets/a46e9ba8-d305-4526-b1c3-6c76f8e9b5ef)

> 📍 [Click here to view the interactive map](map_top_10_stations.html)

This interactive map displays the top 10 start stations for each rider group and offers a spatial perspective on rider behavior.

- **Members** frequently use stations located in Chicago’s downtown business district, such as those near Union Station and the Loop (e.g., *Kingsbury St & Kinzie St*, *Clinton St & Washington Blvd*, *Clark St & Elm St*). This suggests that members likely use the service for commuting or daily routines.
  
- **Casual riders**, however, mostly start and end rides near waterfronts and popular tourist destinations like Navy Pier, Millennium Park, and the Lakefront Trail (e.g., *Streeter Dr & Grand Ave*, *DuSable Lake Shore Dr & Monroe St*). This points to more recreational or sightseeing use.

Popular member stations concentrate around business centers, while stations most used by casual riders align with Chicago’s scenic and tourist-friendly areas. This geographic divide reinforces the idea that members are likely locals using bikes for transportation, while casual riders are more often tourists or infrequent users.


![image](https://github.com/user-attachments/assets/deb34b2a-16dc-444a-b9a7-8e971009b51e)
![image](https://github.com/user-attachments/assets/d9d8fe9a-f54b-41a4-9d0d-9b991ba5e387)

Comparing the percentage of rides from the top 5 starting and ending stations for each group shows another key difference:

-  For **casual riders**, 2.33% of rides start at their most popular station and 2.52% end there
- For **members**, only 0.86% of rides start at their most popular station and 0.87% end there

These statistics support that casual riders tend to concentrate their trips around a few iconic, tourist-heavy locations, while member rides are more evenly distributed across the area. Even though the percentages may seem small, they represent tens of thousands of trips and reveal important differences in how the service is used by each group.

![image](https://github.com/user-attachments/assets/dc30a993-4bf1-40fc-8eff-8bb298c86619)
![image](https://github.com/user-attachments/assets/70cef92a-1290-415b-8e09-1a2b1906e159)

The charts illustrate that members tend to have a more even distribution of rides across a wide range of stations, whereas casual riders concentrate their trips more heavily at just a few popular stations.
- For members, the difference between the first and second most popular start stations is 0.13%
- For casual riders, the difference between the first and second most popular start stations is 0.77%

This larger gap for casual riders reflects a strong preference for select stations, while members’ smaller gap indicates a more diverse usage pattern.

#### Popular Routes

Instead of looking at starting and ending stations separately, I also consider the two together to get a sense of popular routes taken by each group.

![image](https://github.com/user-attachments/assets/b545f694-e93c-40f5-b188-bfd800e0b2c3) 
![image](https://github.com/user-attachments/assets/49d5cdd3-2154-416a-b100-b6b049d31ad6)

I calculate the overall percentage of rides that start and end at the same station for each rider group:

![image](https://github.com/user-attachments/assets/827d8bd3-bdf8-482d-91ac-c476230561ba)

The most common routes for members and casual riders further emphasize the difference in how each group uses the bike-sharing system.

- **Members** tend to ride between different stations (*State St & 33rd St → Calumet Ave & 33rd St*)
- 1.66% of trips started and ended at the same station for members

  These are point-to-point trips often located near universities and residential areas, likely for commuting, classes, or other daily activities.

- **Casual riders** commonly take loop rides (*Streeter Dr & Grand Ave → Streeter Dr & Grand Ave*)
- 5.68% of trips started and ended at the same station for casual riders

  These routes start and end at the same location, indicating leisure or recreational use, often around popular tourist destinations near the lakefront.

This route data further reinforces the broader trend in which members use the bikes as a transportation tool, while casual riders treat it more as a recreational experience.

### 💡 Summary of Findings

This analysis reveals several key insights into how casual riders and members use Cyclistic bikes differently:

| Aspect | Casual Riders | Members |
|--------|---------------|---------|
| Usage Purpose | More likely to use Cyclistic bikes for leisure. | Tend to use the service for commuting. |
| Monthly Trend | Show strong seasonal trends with rides peaking in summer and fall. | Rides also peak in summer and fall, but have somewhat more of a consistent usage year-round. |
| Daily Trend | Rides peak on weekends, especially Saturdays. | Rides peak on weekdays, mostly on Wednesdays. |
| Hourly Trend | Favor midday and afternoon rides. | Ride during typical commute times (morning and evening peaks). |
| Station & Location Usage | Mainly start and end rides near scenic and recreational locations. | Prefer stations near business districts and transit hubs. |
| Route Preferences | Favor leisure-focused loops.  | Use more direct, point-to-point routes between home, work, and transit. |

---

## 🚀 06. Act

### Top 3 Recommendations

#### 1. Launch seasonal membership campaigns at high-traffic tourist areas
- Offer limited-time summer and fall discounts on annual memberships and promote them directly at scenic stations through on-site posters, in-app banners, or QR codes on bikes or kiosks linking to a membership sign-up page.

#### 2. Promote convenience and savings through ride recaps
- After a casual ride, send a follow-up email or app notification showing the cost of their ride compared to what a member would pay and a one-click option to join with a first-month-free trial.

#### 3. Introduce “Flex Memberships” for weekend or leisure riders
- Offer a flexible, lower-commitment membership tier, such as a “Weekend Rider Pass” or a “Seasonal Membership” that gives casual riders access to member pricing and perks.

### Next Steps
Further analysis might include the following:
- A deeper dive into assessing ride duration by bike type and areas of demand for each type
- Explore underperforming stations to identify opportunities for improvement
- Test different pricing models to determine the potential impact on member conversion

---

## 🧠 How to Use This Project

Clone the repository:
   ```bash
   git clone https://github.com/michael22leeapp/cyclistic-analysis-case-study.git
   cd cyclistic-analysis-case-study
   ```
Thanks for reading!
