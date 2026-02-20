# Case Study 2:How Can a Wellness Technology Company Play It Smart
## A Google Data Analytics Professional Certificate Capstone Project

The goal of this case study is to follow the steps of the data analysis process : Ask, Prepare, Process, Analyze, Share and Act. 

<img align="right" width="400" height="200" src="https://github.com/EdwardRook/Case-Study-2-How-Can-a-Wellness-Technology-Company-Play-It-Smart-/blob/main/Bellabeat_logo.webp.png">

[Bellabeat]( https://bellabeat.com/) is a high-tech maufacture of health-focused products for women. Bellabeat's co-founder, Urška Sršen believes that analysising smart device fitness data could help unlock new growth opportunities for the company.

I, a Junior Data Analyst have been asked to analyse data from FitBit to see how consumers are using their smart devices.

## Ask

### Key Stakeholders
1. Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer

2. Sando Mu: Mathematician and Bellabeat’s cofounder

3. The Bellabeat marketing analytics team: a team of data analysts responsible for collecting, analyzing, and reporting data that helps guide Bellabeat’s marketing strategy.

### Bellabeat products

* Bellabeat app: The Bellabeat app provides users with health data related to their activity, sleep, stress, menstrual cycle, and mindfulness habits. This data can help users better understand their current habits and make healthy decisions. The Bellabeat app connects to their line of smart wellness products.

* Leaf: Bellabeat’s classic wellness tracker can be worn as a bracelet, necklace, or clip. The Leaf tracker connects to the Bellabeat app to track activity, sleep, and stress.

* Time: This wellness watch combines the timeless look of a classic timepiece with smart technology to track user activity, sleep, and stress. The Time watch connects to the Bellabeat app to provide you with insights into your daily wellness.

* Spring: This is a water bottle that tracks daily water intake using smart technology to ensure that you are appropriately hydrated throughout the day. The Spring bottle connects to the Bellabeat app to track your hydration levels.

* Bellabeat membership: Bellabeat also offers a subscription-based membership program for users. Membership gives users 24/7 access to fully personalized guidance on nutrition, activity, sleep, health and beauty, and mindfulness based on their lifestyle and goals.

### Business Task 

Analyse FitBit Fitness Tracker data and compare with one Bellabeat product to help guide marketing strategies for the company.

### Key Questions

1. What are some trends in smart device usage? 
2. How could these trends apply to Bellabeat customers? 
3. How could these trends help influence Bellabeat marketing strategy?

## Prepare

### Data Source

FitBit Fitness Tracker Data on [Kaggle]( https://www.kaggle.com/datasets/arashnic/fitbit): This Kaggle data set contains personal fitness tracker data from thirty fitbit users. Thirty eligible Fitbit users consented to the submission of personaltracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users’ habits.

### Limitations

* As we will find with my analysis, the sample size is very small. Hard to draw any real trends from just 30 individuals.

* Data is from 2016, it would not useful 10 years on for analysing current trends.

* Data from the *weight* only has 8 participants and data from *heartrate_seconds* only has 14 participants. Without we have missed out on some really great opportunites to draw helpful conclusions. Such as, does a increased heartrate lead to more calories being burnt.

## Process

I used two tools. RStudio was used to clean and organise my data. Tableau was used to create my visualisations.

### Cleaning and Transforming

1. Install and load the library packages I intend to use for processing and cleaning
2. Assign names to my datasets to make them easier to work with.
3. Check to see if data has loaded correctly.
4. Merge `hourly_calories`, `hourley_intensity`, and `hourly_steps` into one table called `hourly_activity`.
```
hourly_activity <- merge(hourly_intensity, hourly_calories, by=c("Id", "ActivityHour"))
hourly_activity <- merge(hourly_activity, hourly_steps, by=c("Id", "ActivityHour"))
```
5. Clean coloumn names and remove capital letters.
```
# Remove capital letters
daily_activity <- rename_with(daily_activity, tolower)
daily_sleep <- rename_with(daily_sleep, tolower)
hourly_activity <- rename_with(hourly_activity, tolower)

# Check for duplicates
sum(duplicated(daily_activity))
sum(duplicated(daily_sleep))
sum(duplicated(hourly_activity))
```
6. Remove any duplicates found in my datasets. 3 Duplicates were found in `daily_sleep`
```
daily_sleep <- unique(daily_sleep)
sum(duplicated(daily_sleep))
```
7. Add a day of the week variable and sorted from Monday to Sunday
```
daily_activity$day_of_week <- wday(daily_activity$activitydate)
daily_activity <- daily_activity %>%
  mutate(day_of_week = recode(day_of_week
    ,"1" = "Sunday"
    ,"2" = "Monday"
    ,"3" = "Tuesday"
    ,"4" = "Wednesday"
    ,"5" = "Thursday"
    ,"6" = "Friday"
    ,"7" = "Saturday"))

daily_activity$day_of_week <- ordered(daily_activity$day_of_week, levels=c("Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"))
```
8. Add a columns to understand how much daily activity and sleep each participant is getting.
```
daily_activity$total_active_hours = (daily_activity$fairlyactiveminutes  + daily_activity$lightlyactiveminutes + daily_activity$sedentaryminutes + daily_activity$veryactiveminutes)/60
daily_activity$total_active_hours <- round(daily_activity$total_active_hours,2)

daily_sleep$hours_in_bed <- round((daily_sleep$totalminutesasleep)/60,1)
daily_sleep$hours_asleep <- round((daily_sleep$totaltimeinbed)/60,1)
```

## Analyse

### TotalSteps vs Calories Burn
The first piece of analyse looks at the relationship between total steps and calories burned. As you can see there is a positive correlation between the amount of steps a participant takes and the calories that they burn. From this we can draw the conclusion that the more active a person is, the more calories they will burn.

![TotalSteps vs Calories Burn](https://github.com/EdwardRook/Case-Study-2-How-Can-a-Wellness-Technology-Company-Play-It-Smart-/blob/main/TotalSteps%20vs%20Calories.png?raw=true "TotalSteps vs Calories Burn")

### Calories vs Hours Asleep
Interestingly, there is not a postive correlation between hours spent asleep and calories burnt. The idea for this piece of analysis was suggest that the more sleep someone was getting, the more active they would be during there day. From this 30 person dataset, this is not the case. It would be great to get a larger sample size to understand if this remains true, or if there is a 'ideal' amount of sleep a person should get to maximise activity.

![Calories vs Hours Asleep](https://github.com/EdwardRook/Case-Study-2-How-Can-a-Wellness-Technology-Company-Play-It-Smart-/blob/main/Calories%20vs%20Hours%20Asleep.png?raw=true "Calories vs Hours Asleep")

### Average Steps vs Day of Week
It is important to know what days people tend be the most active. The below confirms that Saturday is the 'most' active days when looking at the participants steps. We can also conclude that Sunday is typically used as a reset day where people are taking the most rest. Alongside this, Monday and Tuesday could be seen as the next most active days as people are typically more motivated at the front end of the week, and this naturally trails off as the week processes.

![Average Steps vs Day of Week](https://github.com/EdwardRook/Case-Study-2-How-Can-a-Wellness-Technology-Company-Play-It-Smart-/blob/main/Average%20Steps%20vs%20Day%20of%20Week.png?raw=true "Average Steps vs Day of Week")

### Activity Hour vs Average Step Total
Unsurprisingly, people are the least active within the first 8 hours of the day. However, 9pm and 9am seem to be the most popular times to for people to get their steps in. It would be great to be able to do further analysis into this. Such as, where are the people going at these times? Is 9am activity due to a morning commute, or perphas at 9pm steps are due to an evening run.

![Activity Hour vs Average Step Total](https://github.com/EdwardRook/Case-Study-2-How-Can-a-Wellness-Technology-Company-Play-It-Smart-/blob/main/Activity%20Hour%20vs%20Average%20Step%20Total.png?raw=true "Activity Hour vs Average Step Total")

## Share
Check out my analysis on Tableau Public: [Bellabeat Analysis](https://public.tableau.com/app/profile/edawrd.rook/viz/CaseStudy2HowCanaWellnessTechnologyCompanyPlayItSmart_17715024691580/TotalStepsvsCalories?publish=yes)

## Act
It is paramount to emphasis the sample size of this dataset, alongside this, gender, age, and limited weight details has left this analysis incredibly surface level.

1. 










