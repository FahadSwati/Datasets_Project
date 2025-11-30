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

### Analysis 3: AQI Category Distribution


SELECT 
    AQI_Bucket,
    COUNT(*) AS Count,
    ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(), 2) AS Percentage
FROM city_air_quality
WHERE AQI_Bucket IS NOT NULL
GROUP BY AQI_Bucket
ORDER BY Count DESC;


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




## 📹 **WEEK 10 VIDEO: Visualizations in Zeppelin (Record This!)**

### **PART 7: ZEPPELIN - Create Visualizations (8-10 minutes)**

**Access Zeppelin:**
```
1. Open browser → http://localhost:9995
2. Click "Create new note"
3. Name it: "Air Quality Analysis"
4. Default Interpreter: Keep as "spark2"
5. Click "Create"
```

### Visualization 1: Bar Chart - Top Cities by AQI

%jdbc(hive)

SELECT 
    City,
    AVG(AQI) AS Avg_AQI
FROM city_air_quality
WHERE AQI IS NOT NULL
GROUP BY City
ORDER BY Avg_AQI DESC
LIMIT 10


### Visualization 2: Line Chart - Pollution Trend


%jdbc(hive)

SELECT 
    SUBSTR(Date_Column, 1, 7) AS Month,
    AVG(PM25) AS Avg_PM25,
    AVG(NO2) AS Avg_NO2
FROM city_air_quality
WHERE Date_Column IS NOT NULL
GROUP BY SUBSTR(Date_Column, 1, 7)
ORDER BY Month


### Visualization 3: Pie Chart - AQI Categories


%jdbc(hive)

SELECT 
    AQI_Bucket,
    COUNT(*) AS Count
FROM city_air_quality
WHERE AQI_Bucket IS NOT NULL
GROUP BY AQI_Bucket


### Visualization 4: Table - Health Impact Summary

%jdbc(hive)

SELECT 
    location_name AS Location,
    CAST(AVG(deaths) AS INT) AS Avg_Deaths,
    COUNT(*) AS Records
FROM health_impact
WHERE deaths IS NOT NULL
GROUP BY location_name
ORDER BY Avg_Deaths DESC
LIMIT 15
