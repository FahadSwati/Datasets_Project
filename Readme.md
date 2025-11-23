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




city_air_quality.city	city_air_quality.date_column	city_air_quality.pm25	city_air_quality.pm10	city_air_quality.no	city_air_quality.no2	city_air_quality.nox	city_air_quality.nh3	city_air_quality.co	city_air_quality.so2	city_air_quality.o3	city_air_quality.benzene	city_air_quality.toluene	city_air_quality.xylene	city_air_quality.aqi	city_air_quality.aqi_bucket
Delhi	2015-01-01	153.3	241.7	182.9	33.0	81.3	38.5	1.87	64.5	83.6	18.93	20.81	8.32	204.5	Severe
Mumbai	2015-01-01	70.5	312.7	195.0	42.0	122.5	31.5	7.22	83.8	108.0	2.01	19.41	2.86	60.9	Satisfactory
Chennai	2015-01-01	174.1	275.4	56.2	68.8	230.9	28.5	8.56	60.8	43.9	19.07	10.19	9.63	486.5	Severe
Kolkata	2015-01-01	477.2	543.9	14.1	76.4	225.9	45.6	2.41	42.1	171.1	9.31	11.65	9.39	174.4	Very Poor
Bangalore	2015-01-01	171.6	117.7	123.3	12.4	61.9	49.7	1.26	79.7	164.3	6.04	12.74	9.59	489.7	Good
Delhi	2015-01-02	344.2	173.2	179.8	128.4	156.1	15.6	6.28	7.5	9.4	0.55	15.6	2.45	238.5	Satisfactory
Mumbai	2015-01-02	59.2	335.7	182.1	143.8	207.3	44.8	5.81	6.4	145.5	7.99	29.18	7.11	254.9	Good
Chennai	2015-01-02	418.9	573.8	23.8	17.5	43.0	22.5	4.49	9.5	198.7	1.89	17.93	2.91	171.7	Satisfactory
Kolkata	2015-01-02	329.8	599.3	139.1	34.0	75.2	24.1	3.63	63.4	111.2	8.95	28.51	1.88	139.8	Very Poor
Bangalore	2015-01-02	22.5	317.5	163.0	62.4
