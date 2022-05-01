# Cyclistic-Bike-Share-Study
This corresponds to the first capstone project of the Google Data Analytics Certificate where I performed many real-world tasks of a junior data analyst. I chosed RStudio as my working tool in order to follow the steps of a guided roadmap to complete the data analysis process: ask, prepare, process, analyze, share, and act. 

![]images/graphic 1.jpg

**How Does a Bike-Share Navigate Speedy Success?**

**Scenario**

Jr. Data Analyst working in the marketing analyst team at Cyclistic, a bike-share company in Chicago.

Goal: the company’s future success depends on maximizing the number of annual memberships.

Initial question: how casual riders and annual members use Cyclistic bikes differently?

Objective: design a new marketing strategy to convert casual riders into annual members.

**Characters and teams**

- **Cyclistic:** A bike-share program that features more than 5,800 bicycles and 600 docking stations. Cyclistic sets itself apart by also offering reclining bikes, hand tricycles, and cargo bikes, making bike-share more inclusive to people with disabilities and riders who can’t use a standard two-wheeled bike. The majority of riders opt for traditional bikes; about 8% of riders use the assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use them to commute to work each day.
- **Lily Moreno:** The director of marketing and your manager. Moreno is responsible for the development of campaigns and initiatives to promote the bike-share program. These may include email, social media, and other channels.
- **Cyclistic marketing analytics team:** A team of data analysts who are responsible for collecting, analyzing, and reporting data that helps guide Cyclistic marketing strategy. You joined this team six months ago and have been busy learning about Cyclistic’s mission and business goals — as well as how you, as a junior data analyst, can help Cyclistic
achieve them.
- **Cyclistic executive team:** The notoriously detail-oriented executive team will decide whether to approve the recommended marketing program.

### Business Statement

Cyclistic, a bike-share company in Chicago which future success depends on maximizing the number of annual memberships wants to design a new marketing strategy to convert casual riders into annual members.

### Key Stakeholders

- Lily Moreno, Director of Marketing and Manager.
- Cyclistic Executive Team.
- Cyclistic Marketing Analytics Team.

### 1. Ask:

Three questions will guide the future marketing program:

- How do annual members and casual riders use Cyclistic bikes differently?
- Why would casual riders buy Cyclistic annual memberships?
- How can Cyclistic use digital media to influence casual riders to become members?

### 2. Prepare:

- Data location: in a zip file provided by Cyclistic and made available by Motivate International Inc under a license, it is organized as Structured Data and complies with ROCCC standards, it’s appropiate. It provides the required 12 previous months of Cyclistic’s trip data, where the data collection will be made.
- The integrity can be confirmed by clicking the license link, it then takes you to the Divvy webpage were the data license agreements are stablished under Lyft Bikes and Scooters, LLC (“Bikeshare”) that operates in the City of Chicago’s Divvy bicycle sharing service.
- The few issues found with the data were negative time rides where Divvy took bikes out of circulation for Quality Control reasons and were fixed later.

### 3. Process:

The software used for the analysis was RStudio and the following code and text chunks explain in detail every step taken during the process.

### Install required packages

```
library(tidyverse)  #helps wrangle data
library(lubridate)  #helps wrangle date attributes
library(ggplot2)  #helps visualize data
getwd() #displays your working directory
setwd("/Users/ricardoheredia/cyclistic_project")
```

### COLLECT DATA: upload datasets

```
January_01 <- read_csv("~/Desktop/R data/202101-divvy-tripdata.csv")
February_02 <- read_csv("~/Desktop/R data/202102-divvy-tripdata.csv")
March_03 <- read_csv("~/Desktop/R data/202103-divvy-tripdata.csv")
April_04 <- read_csv("~/Desktop/R data/202104-divvy-tripdata.csv")
May_05 <- read_csv("~/Desktop/R data/202105-divvy-tripdata.csv")
June_06 <- read_csv("~/Desktop/R data/202106-divvy-tripdata.csv")
July_07 <- read_csv("~/Desktop/R data/202107-divvy-tripdata.csv")
August_08 <- read_csv("~/Desktop/R data/202108-divvy-tripdata.csv")
September_09 <- read_csv("~/Desktop/R data/202109-divvy-tripdata.csv")
October_10 <- read_csv("~/Desktop/R data/202110-divvy-tripdata.csv")
November_11 <- read_csv("~/Desktop/R data/202111-divvy-tripdata.csv")
December_12 <- read_csv("~/Desktop/R data/202112-divvy-tripdata.csv")
```

### WRANGLE DATA AND COMBINE IT INTO A SINGLE FILE:

### Compare column names on each of the files

While the names don't have to be in the same order, they do need to match perfectly before we can use a command to join them into one file

```
colnames(January_01)
colnames(February_02)
colnames(March_03)
colnames(April_04)
colnames(May_05)
colnames(June_06)
colnames(July_07)
colnames(August_08)
colnames(September_09)
colnames(October_10)
colnames(November_11)
colnames(December_12)
```

### Inspect the dataframes and look for incongruencies

```
str(January_01)
str(February_02)
str(March_03)
str(April_04)
str(May_05)
str(June_06)
str(July_07)
str(August_08)
str(September_09)
str(October_10)
str(November_11)
str(December_12)
```

### Stack individual monthly data frames into one big data frame

### Remove lat and long columns

```
all_trips <- all_trips %>%
  select(-c(start_lat, start_lng, end_lat, end_lng))
```

### CLEAN UP AND ADD DATA TO PREPARE FOR ANALYSIS:

### Inspect the new table that has been created

```
colnames(all_trips)  #List of column names
nrow(all_trips)  #How many rows are in data frame?
dim(all_trips)  #Dimensions of the data frame?
head(all_trips)  #See the first 6 rows of data frame.
tail(all_trips) 
str(all_trips)  #See list of columns and data types (numeric, character, etc)
summary(all_trips)  #Statistical summary of data.
```

### There are a few problems we will need to fix:

(1) Add some additional columns of data such as day and month that provide additional opportunities to aggregate the data.

(2) We will want to add a calculated field for length of ride so we will add "ride_length" to the entire dataframe for consistency.

(3) There are some rides where tripduration shows up as negative, including several hundred rides where Divvy took bikes out of circulation for Quality Control reasons. We will want to delete these rides.

### Begin by seeing how many observations fall under each usertype

```
table(all_trips$member_casual)
```

### Add columns that list the date, month, day and year of each ride.

```
all_trips$date <- as.Date(all_trips$started_at) #The default format is yyyy-mm-dd
all_trips$month <- format(as.Date(all_trips$date), "%m")
all_trips$day <- format(as.Date(all_trips$date), "%d")
all_trips$day_of_week <- format(as.Date(all_trips$date), "%A")
```

### Add a "ride_length" calculation to all_trips (in seconds)

```
all_trips$ride_length <- difftime(all_trips$ended_at,all_trips$started_at)
```

### Inspect the structure of the columns

```
str(all_trips)
```

### Convert "ride_length" from Factor to numeric so we can run calculations on the data

```
is.factor(all_trips$ride_length)
all_trips$ride_length <- as.numeric(as.character(all_trips$ride_length))
is.numeric(all_trips$ride_length)
```

### Removing "bad" data: the dataframe includes a few hundred entries when bikes were taken out of docks and checked for quality by Divvy or ride_length was negative. We then created a new version of the dataframe (v2) since data is being removed

```
all_trips_v2 <- all_trips[!(all_trips$ride_length<0),]
```

### 4. Analyze:

### CONDUCT DESCRIPTIVE ANALYSIS:

### Descriptive analysis on ride_length (all figures in seconds)

```
mean(all_trips_v2$ride_length) #straight average (total ride length / rides)
median(all_trips_v2$ride_length) #midpoint number in the ascending array of ride lengths
max(all_trips_v2$ride_length) #longest ride
min(all_trips_v2$ride_length) #shortest ride
```

In order to answer the business questions asked at the beginning, an initial descriptive analysis of the data must be done. And the numbers shows that:

- Average of rides were 1316 with a midpoint of 720 between both users.
- The longest ride taken was of 3.356.649 seconds and the shortest was of 0 min meaning that the rider probably did not took off.

### The four lines above were condensed to one line using summary() on the specific attribute

```
summary(all_trips_v2$ride_length)
```

### Compare members and casual users

```
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = mean)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = median)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = max)
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual, FUN = min)
```

### See the average ride time by each day for members vs casual users

```
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
```

### Fix the order of the days of the week.

```
all_trips_v2$day_of_week <- ordered(all_trips_v2$day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
```

### Average ride time by each day for members vs casual users

```
aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
```

### Analyze ridership data by type and weekday

```
all_trips_v2 %>%
  mutate(weekday = wday(started_at, label = TRUE)) %>%  #creates weekday field using wday()
  group_by(member_casual, weekday) %>%  #groups by usertype and weekday
  summarise(number_of_rides = n()	#calculates the number of rides and average duration
  ,average_duration = mean(ride_length)) %>% 		# calculates the average duration
  arrange(member_casual, weekday)								# sorts
```

### 5. Share:

### Visualizations the number of rides by rider type:

```
all_trips_v2 %>%
  mutate(weekday = wday(started_at, label = TRUE)) %>%
  group_by(member_casual, weekday) %>%
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>%
  arrange(member_casual, weekday)  %>%
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")
```

![](images/graphic%201.png)


In the bar chart, we can see the weekend with the most used days for casual members to ride, being Saturday with the highest number of rides with over 450.000 and Sunday in second place with a little more than 400.000 rides. Unlike annual members, who had a higher development on the weekdays, specially on Tuesday, Wednesday and Thursday with numbers close to 400.000 rides and showing less rides on the weekend compared to casual members who traveled less in the weekdays. This might indicate that casual members tends to prefer riding on weekend showing an important difference compared to weekdays. Though annual members show more preference for middle weekdays (surpassing casual members), their performance through the other days did not lowered dramatically.


### Visualization for average duration:

```
all_trips_v2 %>%
  mutate(weekday = wday(started_at, label = TRUE)) %>%
  group_by(member_casual, weekday) %>%
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>%
  arrange(member_casual, weekday)  %>%
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge")
```

### Visualize the number of rides per month by rider type:

```
all_trips_v2 %>%
  mutate(weekday = wday(started_at, label = TRUE)) %>%
  group_by(member_casual, month) %>%
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>%
  arrange(member_casual, month)  %>%
  ggplot(aes(x = month, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge")
```

### EXPORT SUMMARY FILE:

```
counts <- aggregate(all_trips_v2$ride_length ~ all_trips_v2$member_casual + all_trips_v2$day_of_week, FUN = mean)
write.csv(counts, file = '~/Desktop/cyclistic_project/avg_ride_length.csv')
```

### 6. Act:

- How do annual members and casual riders use Cyclistic bikes differently?

Through the first five months of the year 2021 the annual members had an increasing activity on bike rides showing themselves as active subscribers, then in the next three months (June, July and August) that correspond to the summer season, their performance overcame the annual members. This was also demonstrated on the weekdays where they had more number of rides than annual members specifically on the weekend (Saturday and Sunday) and also an increase on the average duration of the trips.

The difference can be seen in the bar charts, where both type of users gradually increased their activity and had peaks of riding during the year, and then it decreased on the last trimester of the year. It did dramatically on behalf of casual members and gradually on annual members side.

This could mean that the users prefer taking more bike rides during the summer rather than the winter and other seasons of the year due to the great weather of summertime, specially the casual members, that might be conformed by a high volume of tourists that travel to the city by that time and take bike tours around the town, increasing the revenue of the company and enhancing the tourism of Chicago. It is also important mentioning that the group of casual members might be also conformed by local citizens that rather travel to work on bike or just use them on their leisure time more often by that time of the year.

- Why would casual riders buy Cyclistic annual memberships?

Because according to the results shown they would benefit on taking the bike rides through the whole year, not only on the peak season such as summer or the month prior to it where they might have to pay for a Daypass or a single ride, so an annual membership would cost them less on a yearly balance either if they want to bikeride for leisure or going to work. That would also mean they can take longer trips on weekends (their preferred ride days) and not worrying about paying a lot more in single passes, meaning they can feel comfortable with an annual membership that saves them money and give them great customer advantage.

- How can Cyclistic use digital media to influence casual riders to become members?

Cyclistic can use their preferred channels to advertise the annual membership to casual riders on specific times of the year where they had more activity such as the end of the week, specially on Saturdays and Sundays and also in the previous months or days to the summer season where the peak of rides was the highest.

References: 'Sophisticated, Clear, and Polished’: Divvy and Data Visualization’ written by Kevin Hartman (found here: [https://artscience.blog/home/divvy-dataviz-case-study](https://artscience.blog/home/divvy-dataviz-case-study))
