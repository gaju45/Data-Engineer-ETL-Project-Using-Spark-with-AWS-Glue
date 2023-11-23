

# Data Engineer ETL Project Using Spark with AWS Glue

## Project Summary:

hello everyone, this is AWS ETL project using Spark and AWS glue. in this project I have basically performed 3 steps Extract, Transform and load that’s why we called it ETL project. i performed these steps with AWS various services like AWS S3, AWS Glue, AWS IAM. with the help of below image you can understand the structure of project.

## What we gonna do step by step

**step 1:** Create IAM Role for whole project.

**step 2:** Create an S3 bucket and load data to the bucket from our local.

**step 3:** Create AWS Glue database and table.

**step 4:** Create ETL Job in AWS Glue

**step 5:** Write transform Code

### Step 1: Create IAM Role

What is IAM Role – An IAM (Identity and Access Management) role in AWS defines permissions for resources and services, allowing them to access specific resources without needing permanent credentials. It enhances security and simplifies access management.

- Go to AWS IAM → Roles → Create Role
- Use cases for other AWS services : Select Glue
- Add permissions → Search and Selec ‘AdministratorAccess’
- Role name : ‘AWSGlueServiceRole-me’

![aws-pipeline](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/6796ccd2-be8a-4988-82fb-15fc45478018)

### Step 2:  We create a bucket in AWS S3

AWS S3 – Amazon S3 is an object storage service offered by AWS. It’s renowned for its high availability, durability, and scalability. In Amazon S3, you can store objects such as images, code files, video files, and even host websites. All data in Amazon S3 is organized into containers called 
“buckets.”

#### Step 2.1: Go to AWS S3 -> Buckets -> Create bucket

- Bucket name: ‘new-york-city-airbnb-2023’

![aws-bucket-airbnb](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/b254d77a-d503-4466-9276-3c269afefa21)

#### Step 2.2: We create database folder

- Go to AWS S3 –> Buckets –> ‘new-york-city-airbnb-2023’ –> Create folder
- Folder name : ‘NYC-airbnb-2023’

![airbnb-folder-in-bucket](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/9453bf07-a542-4dad-bcde-e94d4a1cc858)

#### Step 2.3: We create 2 folder for raw data and transformed data.

- Go to AWS s3 --> Buckets --> 'new-york-city-airbnb-2023' --> 'NYC-airbnb-2023' --> create 2 folders 
- Folder name: 'row-data'
- Folder name: 'transformed-data'

![row-transfromed-data](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/f4f5167a-27b3-4963-9067-b4b3a1aecede)

#### Step 2.4: Now we will upload our local data to S3 'row-data' folder.

- Go to AWS S3 --> Buckets --> 'new-york-city-airbnb-2023' --> 'NYC-airbnb-2023' --> 'row-data' --> click on Upload --> Add Files --> 'NYC-Airbnb-2023.csv'

![nyc-airbnb-2023-csv-file](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/15a42bed-b7c8-4b35-9772-bb321970c984)

here we have completed our 2nd step we successful upload our local data to S3 bucket. next we create AWS database and table.

### Step 3: Set-up AWS Glue Database and table

AWS Glue - AWS Glue is a fully managed ETL (Extract, Transform, Load) service that makes it easier to prepare and move data for analysis. It automates data discovery, transformation, and loading tasks, helping you to create data pipelines and work with data efficiently in the AWS cloud.

#### Step 3.1: Create Database in AWS Glue

- Go to AWS Glue --> Data Catalog --> Database --> Add databaseDatabase name: 'etl-pipeline-project'


![aws-glue-database](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/8ba4cc6f-51d8-491d-bfaa-a6ebd43146ad)


#### Step 3.2: Create Table in Database

- Go to AWS Glue --> Data Catalog --> Tables --> click on 'Add tables using crawler' 

after clicking you have to fill some detail to fetch schema from AWS S3. those detail we have given below.

- Crawler name: ‘elt-airbnb-data-pipeline’
- Data source S3 path: Choose the ‘raw_data’ bucketIAM role --> Choose 
- IAM role --> ’AWSGlueServiceRole-me’
- Target database --> Choose the ‘etl-pipeline-project’
- Schedule : On demand

![table-in-aws-glue-database](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/17a8ac6f-7d18-4a88-a210-dd061f9e850a)

#### Step 3.3: Run the Crawler

after created successful crawler click on Run Crawler to fetch schema of file. our crawler is successfully created. we can check the table and schema of our table.

![AWS-crawler](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/0d924fe8-54ea-4dc1-91a1-24ba8a84c8cd)

![schema-of-csv-file](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/b7e18eb9-504b-47bc-a4b2-2be24099e663)

Step 4: we create ETL Job in AWS Glue

- Go to AWS Glue  -->  Spark script editor -->  click on Create 
Job name: 'myGlueETLjob'
IAM Role --> Choose the role that we created --> 'AWSGlueServiceRole-me'
Type: Spark

![aws-glue-etl-job](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/2788bbd6-e218-4193-87d8-469fa1099882)

### Step 5:  let’s Write Some PySpark Code for Transform the Data.

`import pyspark
from pyspark.sql import SparkSession
from pyspark.sql.functions import * from pyspark.sql.types import *`

`S3_INPUT_DATA = 's3://new-york-city-airbnb-2023/NYC-airbnb-2023/row-data/'`

`S3_OUTPUT_DATA = 's3://new-york-city-airbnb-2023/NYC-airbnb-2023/transformed-data/'`

#### Creating the SparkSession
`spark = SparkSession.builder.master("local[1]")\
.appName('techycrispy.com')\
.getOrCreate()`
                    
#### Spark DataFrame (Raw) - Transformation
`df = spark.read.format("csv")\
.option("header",True)\
.option("inferschema",True)\
.load(S3_INPUT_DATA)`

#### Convert the column's datatype
`df = df.withColumn("id", df['id'].cast(IntegerType())\
.withColumn("host_id",df['host_id'].cast(IntegerType()))\
.withColumn("latitude", df['latitude'].cast(FloatType()))\
.withColumn("longitude", df['longitude'].cast(FloatType()))\
       .withColumn("minimum_nights", df['minimum_nights'].cast(IntegerType()))\
       .withColumn("number_of_reviews",df['number_of_reviews'].cast(IntegerType()))\
       .withColumn("last_review", df['last_review'].cast(DateType()))\
       .withColumn("reviews_per_month",df['reviews_per_month'].cast(IntegerType()))\
.withColumn("calculated_host_listings_count",df['calculated_host_listings_count'].cast(IntegerType()))\
.withColumn("availability_365", df['availability_365'].cast(IntegerType()))\
.withColumn("number_of_reviews_ltm",df['number_of_reviews_ltm'].cast(IntegerType())\
.withColumn("license", df['license'].cast(IntegerType()))\
.withColumn("price", df['price'].cast(IntegerType()))`

#### Remove columns from the dataset that contain a high number of null (missing) values.              
`null_counts = df.select([count(when(col(c).isNull(), c)).alias(c) for c in df.columns]).collect()[0].asDict()
col_to_drop = [k for k, v in null_counts.items() if v >400 ] 
droping = df.drop(col_to_drop)`

#### count rows in dataframe 
`droping.count()`

#### counting room types available
`df1 = df.groupBy("room_type").agg(count("room_type").alias("counted"))
count_room_type = df1.sort(df1["counted"].desc()).limit(4)`

#### total earning by room types
`df2 = df.groupBy("room_type").agg(sum("price").alias("total"))
total_earn_room_type = df2.sort(df2.total.desc()).limit(4)`

#### Save the DataFrames under different folders within the S3_OUTPUT_DATA bucket
`df.write.mode('overwrite').csv(S3_OUTPUT_DATA + "/airbnb-dataframe")
count_room_type.write.mode('overwrite').csv(S3_OUTPUT_DATA + "/count-room-type")
total_earn_room_type.write.mode('overwrite').csv(S3_OUTPUT_DATA + "/total-earn-room-type")
droping.write.mode('overwrite').csv(S3_OUTPUT_DATA + "/droping")`

we write transform code to the AWS S3 in 'transformed-data'  Folder. Let’s check our ‘transformed-data’. 

![transformed-file](https://github.com/gaju45/Data-Engineer-ETL-Project-Using-Spark-with-AWS-Glue/assets/97950473/0b86bd63-b15e-4026-bb6f-3165da55f648)


we can see that transformed in AWS S3 bucket.

### conclusion

I hope you have fully understood this project. You'll find straightforward, explanatory articles on various data science topics on Analytics Vidhya. Don't forget to revisit this site.
