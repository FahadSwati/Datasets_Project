# Step 1: Create local directory
mkdir -p /localDatasets
cd /localDatasets

# Step 2: Verify you're in the right place
pwd

# Step 3: Download Dataset 1 (City Monthly Data - 601 rows)
wget https://raw.githubusercontent.com/FahadSwati/Datasets_Project/main/city_monthly_Dataset_1.csv

# Step 4: Download Dataset 2 (Impact Summary - 549 rows)
wget https://raw.githubusercontent.com/FahadSwati/Datasets_Project/main/impact_summary_Dataset_2.csv

# Step 5: Verify both files downloaded
ls -lh

# Step 6: Check file sizes
du -h *.csv

# Step 7: Count rows in each file
wc -l *.csv

# Step 8: View first 5 lines of each file
head -5 city_monthly_Dataset_1.csv
head -5 impact_summary_Dataset_2.csv





# Step 9: Switch to HDFS user temporarily
su hdfs

# Step 10: Set permissions for all users
hdfs dfs -chmod 777 /user

# Step 11: Exit back to root
exit

# Step 12: Create main project directory
hdfs dfs -mkdir -p /user/hadoop/air-quality-project

# Step 13: Create subdirectories for datasets
hdfs dfs -mkdir -p /user/hadoop/air-quality-project/city-data
hdfs dfs -mkdir -p /user/hadoop/air-quality-project/impact-data
hdfs dfs -mkdir -p /user/hadoop/air-quality-project/output

# Step 14: Verify directory structure created
hdfs dfs -ls /user/hadoop/air-quality-project




# Step 15: Make sure you're in /localDatasets
cd /localDatasets

# Step 16: Upload Dataset 1 to HDFS
hdfs dfs -put city_monthly_Dataset_1.csv /user/hadoop/air-quality-project/city-data/

# Step 17: Upload Dataset 2 to HDFS
hdfs dfs -put impact_summary_Dataset_2.csv /user/hadoop/air-quality-project/impact-data/

# Step 18: Verify both uploads successful
hdfs dfs -ls /user/hadoop/air-quality-project/city-data/
hdfs dfs -ls /user/hadoop/air-quality-project/impact-data/

# Step 19: Check file sizes on HDFS
hdfs dfs -du -h /user/hadoop/air-quality-project/
```

**🎤 What to say in video:**
- "Now uploading the datasets from local filesystem to HDFS"
- "The hdfs dfs -put command transfers files to the distributed filesystem"
- "Both files are now successfully stored in HDFS"

---

### **PART 4: AMBARI - Verify Files (1 minute)**
```
1. Open browser → http://localhost:8080
2. Login: admin / admin
3. Click grid icon (top right) → Select "Files View"
4. Navigate to: /user/hadoop/air-quality-project/
5. Click on "city-data" folder → Show the CSV file
6. Click on "impact-data" folder → Show the CSV file



## Table #1
CREATE EXTERNAL TABLE city_air_quality (
City STRING,
Date_Column STRING,
PM25 DOUBLE,
PM10 DOUBLE,
NO DOUBLE,
NO2 DOUBLE,
NOx DOUBLE,
NH3 DOUBLE,
CO DOUBLE,
SO2 DOUBLE,
O3 DOUBLE,
Benzene DOUBLE,
Toluene DOUBLE,
Xylene DOUBLE,
AQI DOUBLE,
AQI_Bucket STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/user/hadoop/air-quality-project/city-data/'
TBLPROPERTIES ("skip.header.line.count"="1");


## Vrify table #1

SELECT * FROM city_air_quality LIMIT 10;


## reat Table #2

CREATE EXTERNAL TABLE health_impact (
measure_id STRING,
measure_name STRING,
location_id STRING,
location_name STRING,
sex_id STRING,
sex_name STRING,
age_id STRING,
age_name STRING,
cause_id STRING,
cause_name STRING,
rei_id STRING,
rei_name STRING,
metric_name STRING,
year_col INT,
val DOUBLE,
upper_limit DOUBLE,
lower_limit DOUBLE,
metric_id STRING,
number_col DOUBLE,
deaths DOUBLE,
rate DOUBLE,
percent_col DOUBLE
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/user/hadoop/air-quality-project/impact-data/'
TBLPROPERTIES ("skip.header.line.count"="1");


## verify table #2

SELECT * FROM health_impact LIMIT 10;


# QURIS IN HIVE

### Analysis 1: Top 10 Most Polluted Cities

SELECT 
    City,
    AVG(PM25) AS Avg_PM25,
    AVG(NO2) AS Avg_NO2,
    AVG(AQI) AS Avg_AQI,
    COUNT(*) AS Total_Records
FROM city_air_quality
WHERE PM25 IS NOT NULL
GROUP BY City
ORDER BY Avg_AQI DESC
LIMIT 10;

### 🎤 What to say:
"Analysis 1: Identifying the top 10 most polluted cities"
"Grouping by city and calculating average PM2.5, NO2, and AQI"
[Read results] "Delhi has the highest average AQI at X, followed by..."

### Analysis 2: Yearly Pollution Trends

SELECT 
    SUBSTR(Date_Column, 1, 4) AS Year,
    AVG(PM25) AS Avg_PM25,
    AVG(NO2) AS Avg_NO2,
    AVG(AQI) AS Avg_AQI,
    COUNT(*) AS Records
FROM city_air_quality
WHERE Date_Column IS NOT NULL
GROUP BY SUBSTR(Date_Column, 1, 4)
ORDER BY Year;

###  🎤 What to say:
"Analysis 2: Examining pollution trends over time"
"Extracting year from date and calculating yearly averages"
[Read results] "Pollution levels have increased/decreased from 20XX to 20XX"



### Analysis 3: AQI Category Distribution


SELECT 
    AQI_Bucket,
    COUNT(*) AS Count,
    ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2) AS Percentage
FROM city_air_quality
WHERE AQI_Bucket IS NOT NULL
GROUP BY AQI_Bucket
ORDER BY Count DESC;

### 🎤 What to say:
"Analysis 3: Distribution of air quality categories"
"Calculating what percentage of days fall into each AQI bucket"
[Read results] "X% of days have Severe air quality, Y% are Good..."


### Analysis 4: Pollutant Comparison by City

SELECT 
    City,
    AVG(PM25) AS Avg_PM25,
    AVG(PM10) AS Avg_PM10,
    AVG(NO2) AS Avg_NO2,
    AVG(CO) AS Avg_CO
FROM city_air_quality
GROUP BY City
ORDER BY Avg_PM25 DESC
LIMIT 5;

### 🎤 What to say:
"Analysis 4: Comparing different pollutants across top 5 cities"
"Looking at PM2.5, PM10, NO2, and CO levels"


### Analysis 5: Health Impact Analysis

SELECT 
    location_name,
    AVG(deaths) AS Avg_Deaths,
    AVG(rate) AS Avg_Rate,
    COUNT(*) AS Records
FROM health_impact
WHERE deaths IS NOT NULL
GROUP BY location_name
ORDER BY Avg_Deaths DESC
LIMIT 10;

### 🎤 What to say:
"Analysis 5: Health impact - deaths attributed to air pollution by location"
"Identifying regions with highest mortality rates"
[Read results] "X location has highest average deaths at Y per year"


### Analysis 6: Worst Pollution Days

SELECT 
    City,
    Date_Column,
    AQI,
    PM25,
    NO2,
    AQI_Bucket
FROM city_air_quality
WHERE AQI IS NOT NULL
ORDER BY AQI DESC
LIMIT 20;

### 🎤 What to say:
- "Analysis 6: Identifying the worst pollution days"
- "Finding days with highest AQI values"
- [Read results] "The worst day was X date in Y city with AQI of Z"



### **PART 7: ZEPPELIN - Create Visualizations (8-10 minutes)**

**Access Zeppelin:**
```
1. Open browser → http://localhost:9995
2. Click "Create new note"
3. Name it: "Air Quality Analysis"
4. Default Interpreter: Keep as "spark2"
5. Click "Create"
```

### Visualization 1: Multi-Pollutant Comparison (Bar Chart)

%jdbc(hive)

SELECT 
    City,
    AVG(PM25) AS PM2_5,
    AVG(PM10) AS PM10,
    AVG(NO2) AS NO2,
    AVG(SO2) AS SO2,
    AVG(CO) AS CO
FROM city_air_quality
WHERE PM25 IS NOT NULL
GROUP BY City
ORDER BY PM2_5 DESC
LIMIT 8

### After running:
Click bar chart icon
Keys: City
Values: Select ALL pollutants (PM2_5, PM10, NO2, SO2, CO)
Settings → Enable "Stacked" for grouped bars 
 ### 🎤 Say in video:
"This grouped bar chart compares multiple pollutants across cities"
"Delhi shows highest levels across all pollutants"

###  Visualization 2: Trend Over Time (Line Chart)


%jdbc(hive)

SELECT 
    SUBSTR(Date_Column, 1, 7) AS Month_Year,
    AVG(PM25) AS PM2_5,
    AVG(NO2) AS Nitrogen_Dioxide,
    AVG(AQI) AS Air_Quality_Index
FROM city_air_quality
WHERE Date_Column IS NOT NULL
GROUP BY SUBSTR(Date_Column, 1, 7)
ORDER BY Month_Year

### After running:

Click line chart icon
Keys: Month_Year
Values: All three metrics (PM2_5, Nitrogen_Dioxide, Air_Quality_Index)
This creates a multi-line graph

### 🎤 Say in video:
"This line chart reveals seasonal patterns in air pollution"
"We can see pollution peaks during winter months"


### Visualization 3: AQI Category Distribution (Pie Chart)

%jdbc(hive)

SELECT 
    AQI_Bucket AS Category,
    COUNT(*) AS Days
FROM city_air_quality
WHERE AQI_Bucket IS NOT NULL
GROUP BY AQI_Bucket
ORDER BY Days DESC

### After running:
Click pie chart icon
Keys: Category
Values: Days

### 🎤 Say in video:
"Pie chart showing distribution of air quality days"
"X% of days have hazardous air quality - a serious health concern"


### Visualization 4: City-by-City AQI Comparison (Horizontal Bar)

%jdbc(hive)

SELECT 
    City,
    CAST(AVG(AQI) AS INT) AS Average_AQI,
    CAST(MAX(AQI) AS INT) AS Worst_Day_AQI,
    COUNT(*) AS Total_Days
FROM city_air_quality
WHERE AQI IS NOT NULL
GROUP BY City
ORDER BY Average_AQI DESC
LIMIT 10

### After running:

Click bar chart icon
Keys: City
Values: Average_AQI, Worst_Day_AQI
Settings → Change orientation to horizontal

### 🎤 Say in video:
"Comparing both average and worst-day AQI for each city"
"Shows both typical conditions and extreme pollution events"


### Visualization 5: Before/After Comparison (Area Chart)

%jdbc(hive)

SELECT 
    SUBSTR(Date_Column, 1, 7) AS Month,
    AVG(CASE WHEN SUBSTR(Date_Column, 1, 4) < '2018' THEN PM25 END) AS Before_2018,
    AVG(CASE WHEN SUBSTR(Date_Column, 1, 4) >= '2018' THEN PM25 END) AS After_2018
FROM city_air_quality
WHERE Date_Column IS NOT NULL
GROUP BY SUBSTR(Date_Column, 1, 7)
ORDER BY Month

### After running:

Click area chart icon
Keys: Month
Values: Before_2018, After_2018

### 🎤 Say in video:
"Area chart comparing PM2.5 levels before and after 2018"
"Helps evaluate impact of pollution control policies"


### Visualization 6: Pollutant Heatmap Table

%jdbc(hive)

SELECT 
    City,
    CAST(AVG(PM25) AS INT) AS PM2_5,
    CAST(AVG(PM10) AS INT) AS PM10,
    CAST(AVG(NO2) AS INT) AS NO2,
    CAST(AVG(AQI) AS INT) AS AQI,
    CASE 
        WHEN AVG(AQI) > 300 THEN 'Hazardous'
        WHEN AVG(AQI) > 200 THEN 'Very Poor'
        WHEN AVG(AQI) > 100 THEN 'Poor'
        ELSE 'Moderate'
    END AS Status
FROM city_air_quality
WHERE PM25 IS NOT NULL
GROUP BY City
ORDER BY AQI DESC

### After running:

Keep as table view
Click settings
Enable conditional formatting (if available)

### 🎤 Say in video:
"Summary table with color-coded air quality status"
"Quick overview of pollution levels across all cities"


### Visualization 7: Top vs Bottom Cities (Split Bar)

%jdbc(hive)

SELECT 
    City,
    AVG(AQI) AS Average_AQI
FROM city_air_quality
WHERE City IN (
    SELECT City 
    FROM (
        SELECT City, AVG(AQI) AS avg_aqi
        FROM city_air_quality 
        WHERE AQI IS NOT NULL
        GROUP BY City
        ORDER BY avg_aqi DESC
        LIMIT 5
    ) top5
    UNION ALL
    SELECT City 
    FROM (
        SELECT City, AVG(AQI) AS avg_aqi
        FROM city_air_quality 
        WHERE AQI IS NOT NULL
        GROUP BY City
        ORDER BY avg_aqi ASC
        LIMIT 5
    ) bottom5
)
GROUP BY City
ORDER BY Average_AQI DESC

### After running:

Click bar chart icon
Shows clear contrast between best and worst cities

### 🎤 Say in video:
"Comparing the 5 most and 5 least polluted cities"
"Stark difference between cleanest and most polluted areas"





## Shriya

wget "https://raw.githubusercontent.com/FahadSwati/Datasets_Project/main/Brasilia_Air_Quality%20(2).csv" -O Brasilia_Air_Quality_2.csv



CREATE EXTERNAL TABLE brasilia_air_quality (
    City STRING,
    Date_Column STRING,
    PM25 DOUBLE,
    PM10 DOUBLE,
    NO DOUBLE,
    NO2 DOUBLE,
    NOx DOUBLE,
    NH3 DOUBLE,
    CO DOUBLE,
    SO2 DOUBLE,
    O3 DOUBLE,
    Benzene DOUBLE,
    Toluene DOUBLE,
    Xylene DOUBLE,
    AQI DOUBLE,
    AQI_Bucket STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/user/hadoop/air-quality-project/brasilia-data/'
TBLPROPERTIES ("skip.header.line.count"="1");

