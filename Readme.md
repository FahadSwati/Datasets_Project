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






health_impact.measure_id	health_impact.measure_name	health_impact.location_id	health_impact.location_name	health_impact.sex_id	health_impact.sex_name	health_impact.age_id	health_impact.age_name	health_impact.cause_id	health_impact.cause_name	health_impact.rei_id	health_impact.rei_name	health_impact.metric_name	health_impact.year_col	health_impact.val	health_impact.upper_limit	health_impact.lower_limit	health_impact.metric_id	health_impact.number_col	health_impact.deaths	health_impact.rate	health_impact.percent_col
1	Deaths	4841	Andhra Pradesh	3	Both	37	20+ years	294	All causes	85	Air pollution	1	null	2015.0	66617.10527047407	77212.47330284558	54687.99512817299	null	null	null	null
1	Deaths	4841	Andhra Pradesh	3	Both	37	20+ years	294	All causes	85	Air pollution	2	null	2015.0	0.168784361779867	0.1934729666793971	0.1379701807846659	null	null	null	null
1	Deaths	4841	Andhra Pradesh	3	Both	37	20+ years	294	All causes	85	Air pollution	3	null	2015.0	191.55642530635	222.02323734878001	157.25445906708998	null	null	null	null
1	Deaths	4841	Andhra Pradesh	3	Both	37	20+ years	294	All causes	85	Air pollution	1	null	2016.0	67890.02090244256	79326.61188147124	55178.214022581655	null	null	null	null
1	Deaths	4841	Andhra Pradesh	3	Both	37	20+ years	294	All causes	85	Air pollution	2	null	2016.0	0.1694294167254803	0.1955710596776405	0.1374754997874948	null	null	null	null
1	Deaths	4841	Andhra Pradesh	3	Both	37	20+ years	294	All causes	85	Air pollution	3	null	2016.0	192.52871761966	224.96164613940002	156.47941545896	null	null	null	null
1	Deaths	4841	Andhra Pradesh	3	Both	37	20+ years	294	All causes	85	Air pollution	1	null	2017.0	68452.13268478114	79927.13249995056	55993.02009991316	null	null	null	null
1	Deaths	4841	Andhra Pradesh	3	Both	37	20+ years	294	All causes	85	Air pollution	2	null	2017.0	0.1685263904668176	0.1931521157027456	0.1369354968321531	null	null	null	null
1	Deaths	4841	Andhra Pradesh	3	Both	37	20+ years	294	All causes	85	Air pollution	3	null	2017.0	191.54888871807	223.65926098006003	156.68468396005	null	null	null	null
1	Deaths	4841	Andhra Pradesh	3
