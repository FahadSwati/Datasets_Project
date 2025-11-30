# Step 1: Make sure you're in /localDatasets
cd /localDatasets

# Step 2: Delete old local files
rm -f city_day_Dataset_1.csv
rm -f impact_Dataset_2.csv

# Step 3: Delete old HDFS files
hdfs dfs -rm -r /user/hadoop/air-quality-project/city-data/*
hdfs dfs -rm -r /user/hadoop/air-quality-project/impact-data/*

# Step 4: Verify everything is deleted
ls -la
hdfs dfs -ls /user/hadoop/air-quality-project/city-data/
hdfs dfs -ls /user/hadoop/air-quality-project/impact-data/
