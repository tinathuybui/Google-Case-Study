# Google Data Analytics Capstone: Bellabeat Case Study
##### Author: Tina Bui
##### Date: June 30, 2022
###  [Introduction](#1-introduction)
###  [Product range](#2-product-range)
###  [Business Task](#3-business-task)
###  [About the Data](#4-about-the-data)
###  [Data Analyse](#5-data-analyse)
###  [Visualization](#6-visualization)
###  [Findings](#7-findings)
###  [Recommendations](#7-recommendation)
###  [Bibliograohy](#7-bibliograohy)

## 1. Introduction


Bellabeat is a US-based company that manufactures high-tech health-focused products specialized for women. Founded in 2013, this successful small company has the potential to grow rapidly in the global smart device market. 


## 2. Product range

Bellabeat offers a range of wireless wearable devices such as Leaf and Time. The leaf is one of Bellabeat's classic wellness trackers. It can be worn as a bracelet, necklace, or clip. Time is a hybrid wellness watch. Both are compact with smart technology that can be connected to the Bellabeat app to track user activity, sleep, and stress. To help users build a healthy hydration habit, Spring was introduced as one of Beallabeat's unique products that were designed in the form of a water bottle to track daily water intake. All of Bellabeat's smart wellness products can be connected to the Bellabeat app to help users understand their current habits and make healthy decisions by displaying health data related to the user's activity, sleep, stress, menstrual cycle, and mindfulness habits. 

## 3. Business Task

Analyze smart device usage data in order to gain insight into how consumers are using their smart devices. From this, recommendations will be given to Urška Sršen and Bellabeat's marketing strategy team on how these trends can shape the company's marketing strategy. 

Key Stakeholders

[Back to Top](#author-tina-bui)

Urška Sršen: Bellabeat's co-founder and Chief Creative Officer 		

Sando Mur: Mathematician and Bellabeat's co-founder; a key member of the Bellabeat executive team

Belllabeat’s marketing team


## 4. About the Data

  Data source
     
The data used in this case study, [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit), is a public dataset on Kaggle. It was generated by the respondent to a distributed survey via Amazon Mechanical Turk for a period of 60 days starting from 12th March 2016 to 12th May 2016. 
This Kaggle data set contains a personal fitness tracker from 30 Fitbit users. There are 18 CSV files, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users' habits.

  Data Limitation

- The data does not contain key demographics about the users, such as sex, age, and ethnicity group.

- The data were historical data back in 2016. It might not reflect the recent trend.

- The sample size is 30, which is the smallest sample size based on Central Limit Theorem. According to Statista, there were 23.2 million active users of Fitbit in 2016. Using Raosoft to calculate the sample size with a confident level of 95%, the sample size would need to be at least 385. This means the data has a high margin of error.

- The data is inconsistent and not complete. There are 33 unique IDs which is greater than the number of users, 30. There are only 24 users who tracked their sleep, and eight users recorded their weight and BMI using Fitbit. This reduces the data reliability level.

<img width="1303" alt="Screen Shot 2022-06-27 at 12 38 26 am" src="https://user-images.githubusercontent.com/89682120/175819662-d85f866f-259a-4dd4-adef-36256c7ed433.png">

  Datastore
  
[Back to Top](#author-tina-bui)

Create a local folder containing 6 CSV tables that will be used in this analyse:

<img width="911" alt="Screen Shot 2022-06-27 at 12 45 54 am" src="https://user-images.githubusercontent.com/89682120/175820015-0cd96076-8cf9-4ebd-8c42-1bba853cc288.png">

Data Tools

- Google Sheets was used to cleaning the data. 
- The data was then analysed in Bigquery.
- The final visualization was created in Tableau public.

Data Cleaning	

- Created a Google Drive folder called Case Study. 
- Uploaded the raw data to Google Drive. 
- Clear formatting
- Convert the SleepDate and Date column in sleepDay_merged.csv and weightLogInfo_merged.csv to custom Date and time formats Month/Date/Year
- Remove time, AM, and PM indicators to keep the data consistent with other tables. 
- Three duplicate records were identified in the sleepDay_merged table. 
- Remove these duplicates using Google Sheet's cleanup suggestions function. 
- Download the data in CSV format to prepare to upload to BigQuery

## 5. Data Analyse

[Back to Top](#author-tina-bui)


- In BigQuery, create a Project named "Bellabeat". Upload and rename 6 CSV tables to the BigQuery platform.
Review Schema and preview data in each table before starting analyses
- Review Schema and preview data in each table before starting analyses


5.1 Check for distinct ID:
```sql
SELECT Count(Distinct ID) FROM `bellabeat-353112.Bella_beat.daily_Activity`;
SELECT Count(Distinct ID) FROM `bellabeat-353112.Bella_beat.dailyCalories` ;
SELECT Count(Distinct ID) FROM `bellabeat-353112.Bella_beat.daily_Intensities`;
SELECT Count(Distinct ID) FROM `bellabeat-353112.Bella_beat.daily_Steps`;
SELECT Count(Distinct ID) FROM `bellabeat-353112.Bella_beat.sleep_day`;
SELECT Count(Distinct ID) FROM `bellabeat-353112.Bella_beat.weight_log`;
```
Results:
33
33
33
33
24
8

5.2 Find the relationship between calories burned, daily activities, and hours of sleep:

```sql
SELECT dc.Calories,
        dc.Id,
        dc.ActivityDay,
        da.TotalSteps,
        da.VeryActiveDistance,
        da.ModeratelyActiveDistance,
        da.LightActiveDistance,
        (sd.TotalMinutesAsleep/60) AS Sleephours
FROM `bellabeat-353112.Bella_beat.dailyCalories` AS dc
LEFT JOIN `bellabeat-353112.Bella_beat.daily_Activity` AS da
   ON dc.Id = da.Id
       AND dc.Calories = da.Calories
LEFT JOIN `bellabeat-353112.Bella_beat.sleep_day` AS sd
   ON dc.Id = sd.Id
       AND dc.ActivityDay = sd.SleepDay;
  ```

Result returned some NULL data in the Sleephours column. Add WHERE clause to filter out all NULL data.

```sql
SELECT dc.Calories,
        dc.Id,
        dc.ActivityDay,
        da.TotalSteps,
        da.VeryActiveDistance,
        da.ModeratelyActiveDistance,
        da.LightActiveDistance,
        (sd.TotalMinutesAsleep/60) AS Sleephours
FROM `bellabeat-353112.Bella_beat.dailyCalories` AS dc
LEFT JOIN `bellabeat-353112.Bella_beat.daily_Activity` AS da
   ON dc.Id = da.Id
       AND dc.Calories = da.Calories
LEFT JOIN `bellabeat-353112.Bella_beat.sleep_day` AS sd
   ON dc.Id = sd.Id
       AND dc.ActivityDay = sd.SleepDay
WHERE (sd.TotalMinutesAsleep/60) is NOT NULL;
```






