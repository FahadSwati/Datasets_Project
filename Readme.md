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
