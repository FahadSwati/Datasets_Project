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
