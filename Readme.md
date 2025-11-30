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





[root@sandbox-hdp localDatasets]# hdfs dfs -rm -r /user/hadoop/air-quality-project/city-data/*
rm: Call From sandbox-hdp.hortonworks.com/172.18.0.3 to sandbox-hdp.hortonworks.com:8020 failed on connection exception: java.net.ConnectException: Connection refused; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused
[root@sandbox-hdp localDatasets]# hdfs dfs -rm -r /user/hadoop/air-quality-project/city-data/*
rm: Call From sandbox-hdp.hortonworks.com/172.18.0.3 to sandbox-hdp.hortonworks.com:8020 failed on connection exception: java.net.ConnectException: Connection refused; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused
[root@sandbox-hdp localDatasets]# hdfs dfs -rm -r /user/hadoop/air-quality-project/impact-data/*
rm: Call From sandbox-hdp.hortonworks.com/172.18.0.3 to sandbox-hdp.hortonworks.com:8020 failed on connection exception: java.net.ConnectException: Connection refused; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused
[root@sandbox-hdp localDatasets]# la -lh
-bash: la: command not found
[root@sandbox-hdp localDatasets]# ls -lh
total 0
[root@sandbox-hdp localDatasets]# hdfs dfs -ls /user/hadoop/air-quality-project/city-data/
ls: Call From sandbox-hdp.hortonworks.com/172.18.0.3 to sandbox-hdp.hortonworks.com:8020 failed on connection exception: java.net.ConnectException: Connection refused; For more details see:  http://wiki.apache.org/hadoop/ConnectionRefused
[root@sandbox-hdp localDatasets]#
