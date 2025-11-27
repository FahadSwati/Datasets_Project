# Download Dataset 1: city_day_Dataset_1.csv
wget https://raw.githubusercontent.com/FahadSwati/Datasets_Project/main/city_day_Dataset_1.csv

# Download Dataset 2: impact_Dataset_2.csv
wget https://raw.githubusercontent.com/FahadSwati/Datasets_Project/main/impact_Dataset_2.csv

# Verify both files downloaded successfully
ls -lh


# Check file sizes
du -h *.csv

# View first few lines to verify data
head -5 city_day_Dataset_1.csv
head -5 impact_Dataset_2.csv

# Count rows in each file
wc -l *.csv


# Make sure you're in the right directory
cd /localDatasets

# Switch to HDFS user temporarily
su hdfs

# Set permissions
hdfs dfs -chmod 777 /user

# Exit back to root
exit

# Create HDFS directory structure
hdfs dfs -mkdir -p /user/hadoop/air-quality-project
hdfs dfs -mkdir -p /user/hadoop/air-quality-project/city-data
hdfs dfs -mkdir -p /user/hadoop/air-quality-project/impact-data
hdfs dfs -mkdir -p /user/hadoop/air-quality-project/output

# Verify directories created
hdfs dfs -ls /user/hadoop/air-quality-project



# Make sure you're in /localDatasets directory
cd /localDatasets

# Upload Dataset 1 (city air quality data)
hdfs dfs -put city_day_Dataset_1.csv /user/hadoop/air-quality-project/city-data/

# Upload Dataset 2 (health impact data)
hdfs dfs -put impact_Dataset_2.csv /user/hadoop/air-quality-project/impact-data/

# Verify uploads were successful
hdfs dfs -ls /user/hadoop/air-quality-project/city-data/
hdfs dfs -ls /user/hadoop/air-quality-project/impact-data/

# Check file sizes on HDFS
hdfs dfs -du -h /user/hadoop/air-quality-project/


#Table_1 for first data 

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


SELECT * FROM city_air_quality LIMIT 10;

#Table_2 for second data


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


# Query no 1

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

# part no 2

SELECT 
    City,
    ROUND(AVG(PM25), 2) AS Avg_PM25,
    ROUND(AVG(NO2), 2) AS Avg_NO2,
    ROUND(AVG(AQI), 2) AS Avg_AQI,
    COUNT(*) AS Total_Records
FROM city_air_quality
WHERE PM25 IS NOT NULL
GROUP BY City
ORDER BY Avg_AQI DESC
LIMIT 10;









# Navigate to local directory
cd /localDatasets

# Remove old files
rm city_day_Dataset_1.csv
rm impact_Dataset_2.csv

# Download new files from GitHub (use your updated GitHub links)
wget https://raw.githubusercontent.com/FahadSwati/Datasets_Project/main/city_monthly_Dataset_1.csv
wget https://raw.githubusercontent.com/FahadSwati/Datasets_Project/main/impact_summary_Dataset_2.csv

# Upload to HDFS
hdfs dfs -put city_monthly_Dataset_1.csv /user/hadoop/air-quality-project/city-data/
hdfs dfs -put impact_summary_Dataset_2.csv /user/hadoop/air-quality-project/impact-data/

# Verify upload
hdfs dfs -ls /user/hadoop/air-quality-project/city-data/
hdfs dfs -ls /user/hadoop/air-quality-project/impact-data/

DROP TABLE city_air_quality;
DROP TABLE health_impact;



