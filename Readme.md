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
