# Automated-Data-Pipeline-for-Temperature-and-Immigration-information

## Project Summary
1. Analyze, Explore and Validate i94 dataset
2. Focus on infrastructure setup for the data pipelines, this involves,
   * Using AWS Cloud formation to start EC2 instance, install Airflow in it, start EMR        and Redshift
   * Installing Airflow and create EMR via Airflow to process data and Terminate EMR          after finishing the processing.
   * Both the above infra setup has it own usecase of which is efficient like,
         * When you use Cloud formation, you have the ability to delete all the                    resources it had created in one-click. So, for testing scenarios this is very            efficient
         * Second method, thats more real-time, that what a company would use. Start a              EMR before processing and process data and Terminate EMR when done. Its very            clean.
3. Visualization, efficiently presenting analyzed information in a stunning manner which is easy to understand.

I am going to take this opportunity to work on point(1) and (3) from above, leaving the airflow & EMR for below reasons :

1. Airflow Installation problems
   * When directly installed in a EC2 instance, getting errors for subdags
   * Shifted from direct install to Docker puckel/airflow, simple dags, getting data          from Redshift are all working but i started to get lots of errors during EMR            creation and eventually thought this is delaying the project.
   
2. AWS Cloud formation
   * I was able to successfully setup SecurityGroups, EC2 instance, Install Airflow in        EC2 instance, Create Redshift cluster and EMR as well.
   * Again had some issues connecting to EMR and for same reason as above quitting this      approach as well. Of these two solutions, i like the Cloud formation approach, due      to the ability to delete all the resources in 1-Click.
   
Getting the infra setup of airflow out of the picture, gives me more time to analyze, validate i94 and other datasets. Gathering the experience from previous projects in the technical side, Pandas are good in analyzing and Jupyter notebook is very good in executing the scripts step by step and lazy evaluation of spark takes lots of time during testing even with 10k-50k rows dataset. So approach i had taken is to use Pandas for all the inital assessment, cleaning and finally analyze if Spark will be required to process any big datasets. If big dataset is found, convert Panda codes to Spark codes, this is an additional step i am willing to take. So, finally all small datasets will be processed by Pandas and big datasets will be processed by Spark(PySpark).

## Technical Overview
Project uses following technologies,

1. S3 Storage : To store inputs & outputs
2. AWS Redshift as Warehousing database for Analytics
3. Python
4. Libraries : Pandas & Pyspark

## Files in the folder
   * i94 SAS datasets provided by Udacity ( around 6GB )
   
root@1150c8c516d1:/home/workspace# du -h ../../data/18-83510-I94-Data-2016/*
451M    ../../data/18-83510-I94-Data-2016/i94_apr16_sub.sas7bdat
597M    ../../data/18-83510-I94-Data-2016/i94_aug16_sub.sas7bdat
500M    ../../data/18-83510-I94-Data-2016/i94_dec16_sub.sas7bdat
374M    ../../data/18-83510-I94-Data-2016/i94_feb16_sub.sas7bdat
415M    ../../data/18-83510-I94-Data-2016/i94_jan16_sub.sas7bdat
621M    ../../data/18-83510-I94-Data-2016/i94_jul16_sub.sas7bdat
684M    ../../data/18-83510-I94-Data-2016/i94_jun16_sub.sas7bdat
459M    ../../data/18-83510-I94-Data-2016/i94_mar16_sub.sas7bdat
501M    ../../data/18-83510-I94-Data-2016/i94_may16_sub.sas7bdat
424M    ../../data/18-83510-I94-Data-2016/i94_nov16_sub.sas7bdat
531M    ../../data/18-83510-I94-Data-2016/i94_oct16_sub.sas7bdat
543M    ../../data/18-83510-I94-Data-2016/i94_sep16_sub.sas7bdat

Ignored folders are,

1. metastore_db : System folder
2. node_modules : created by NPM install

Click here to see the TREE view of folders

## How to run

1. Open ./aws/aws-capstone.cfg and key in KEY, SECRET and KEYSECRET
2. Open Terminal and install

pip install fuzzywuzzy[speedup]

3. Delete folders ./outputs & ./outputs-gzip/
rm -r ./outputs
rm -r ./outputs-gzip/

Execute the below commands in Python 3 console

%run process-df.py
%run S3Upload.py
%run create_cluster.py
%run create_tables.py
%run etl.py
Execute all the cells in Capstone Project - D - Analytics.ipynb
%run delete_cluster.py

## Outputs

1. process-df.py
Click to see console messages

2. S3Upload.py
Click to see console messages

3. create_cluster.py
Click here to see console messages

4. create_tables.py
Click here to see console messages

5. etl.py
Click here to see console messages

## Table of contents

Step 1: Scope the Project and Gather Data

Step 2: Explore and Assess the Data
   * Immigration data - df_ids
   * Country code - df_i94countrycode
   * Port of entry - df_USPoE
   * US Zipcodes - df_uszips
   * Airport codes - df_ac
   * US Cities demographic - df_uscd
   * Temperature - df_temper
   * After Exploring, Assessment & Cleaning
   
Step 3: Define the Data Model

Step 4: Run ETL to Model the Data

Step 5: Complete Project Write Up

## Step 1: Scope the Project and Gather Data
## Scope

This is Udacity Capstone project for Data Engineering. In this project, we will be gathering, assessing, cleaning, creating data models, setup and push data to a data warehouse where it will be analyzed to produce informative reports.

Above raw datasets will be cleaned, enriched to add more information and will be loaded into a data warehouse for this we will use following tools & technologies,

1. Python
2. Juypter Notebooks
3. AWS S3
4. AWS Redshift

## Describe and Gather Data
## Below are the major datasets used in the project,

Source name	Filename	Format	Description
I94 Immigration Sample Data	immigration_data_sample.csv	csv	This is a sample data which is from the US National Tourism and Trade Office.
I94 Immigration Data	../../data/18-83510-I94-Data-2016/i94_apr16_sub.sas7bdat	SAS	This data comes from the US National Tourism and Trade Office.
World Temperature Data	world_temperature.csv	csv	This dataset contains temperature data of various cities from 1700's - 2013. This dataset came from Kaggle.
U.S. City Demographic Data	us-cities-demographics.csv	csv	This dataset contains population details of all US Cities and census-designated places includes gender & race informatoin. This data came from OpenSoft.
Airport Codes	airport-codes_csv.csv	csv	This is a simple table of airport codes and corresponding cities.
I94CIT & I94RES	i94cit.json	json	Shows corresponding i94 Country of Citizenship & Country of Residence codes. Source : I94_SAS_Labels_Descriptions.SAS
I94PORT	i94port.json	json	Shows US Port of Entry city names and their corresponding codes. Source : I94_SAS_Labels_Descriptions.SAS
I94ADDR	i94addr.json	json	Shows US State codes. Source : I94_SAS_Labels_Descriptions.SAS

Other smaller & additional datasets are,

Source name	Filename	Format	Description
Visa Type	visatype2.csv	csv	Contains US Visa types codes. This dataset is manually prepared by getting the data from multiple sites like below, IRS:Taxation of Aliens by VISA Type and Immigration Status , INS_CLASS_of_Admissions.pdf , USCIS I-94 Table.pdf , [Reading I-94.pdf](https://www.dshs.wa.gov/sites/default/files/ESA/eaz-manual/Reading%20I-94.pdf]
Airline Codes	airline-codes.csv	csv	Airliner codes taken from multiple sites like Airline Codes 1 , Github: CSV from beanumber
Visa Post	visapost.csv	csv	Visa issuing post symbols
US Zipcodes	uszips.csv	csv	US Zipcodes database
US Cities	uscities.csv	csv	US Cities database
Country codes 1	country-codes.csv	csv	Country Codes 1
Country codes 2	country-codes2.csv	csv	Country Codes 2
International Airports	InternationalAirports.csv	csv	Manually prepared from wikipedia(Unicode conversion issues might be here)
List of continent codes	stored in dictionary	-	dictionary contains code and respective continent name
SimpleMaps : World Cities Database	worldcities.csv	csv	
US States	us-states.csv	csv	US States code U.S. postal abbreviations , United States of America (US) - State/Province Table , State, Province, and Territory Codes for the United States and Canada
Other unused datasets	-	-	Homeland Infrastructure Foundation-Level Data (HIFLD) , HIFLD - US Coast Guard (USCG) Maritime Differential GPS (DGPS) Locations

## Step 2: Explore and Assess the Data

After exploration & assessment below data quality issues, missing values, duplicates and other issues have been identified

  * fact_ids : Processed via Pyspark as its a huge data
    * invalid : matflag is null
    * invalid : visatype GMT
    * invalid : i94mode other than 1, 2, 3 can be removed.
    * incomplete : gender is null
    * invalid : Remove rows having invalid CoC & CoR
    * invalid : Remove Non-US Port of entry data
    * invalid : Delete Port of Entries which lacks International Passenger Handling           Facility operated by US Customs.
    * Keeping only these columns keep_columns = [i94cit, i94res, i94port, arrdate,             i94mode, i94addr, depadate, i94bir, i94visa, dtadfile, visapost, occup, biryear,         dtaddto, gender, airline, admnum, fltno, visatype]
    * Convert these columns (i94cit, i94res, arrdate, i94mode, depdate, i94bir, i94visa,       biryear, admnum)
    * i94mode - How they arrived - arrival_mode
    * i94visa - Purpose of visit - visit_purpose
    * Convert date formats
      (+) SAS encoded date ( arrdate & depdate )
      (+) For column dtaddto from mmddyyyy to yyyy-mm-dd)
    * Rename columns (i94bir=age, i94cit=CoC, i94res=CoR, i94port=PoE,                         i94addr=landing_state)
    * Adding a new column(entry_exit) to determine the row is ENTRY or EXIT of a person       from US. If there is no departure_dt, it states person is in U.S
    * As per i94 there can be three categories for gender M/F/O and in the i94 rows, its       found there are rows with gender X(third gender). Those rows are updated as O
    * Drop final set columns i94mode, i94visa, arrdate, depdate, dtadfile, dtaddto

  * dim_visatype : Processed by pandas
    * code column contains spaces, it needs to be stripped.

  * dim_ac : Processed by pandas
    * Keep only US Airports
    * Correcting wrong continent value
    * Eliminate rows which has iata code as null
    * Keep only airport which is of type small_airport, medium_airport & large_airport
    * Removing rows having 'Duplicate' in airport names
    * Dropping unused columns gps_code, ident
    * Split geo-coordinates into separate columns
    * Extract state code from iso_region(country-state)
    * Add new column facilities to indicate whether airport has Customs & Immigration

  * dim_USPoE : Processed by pandas
    * Split column city,state to city and state
    * Correct incorrect state code values
    * state with null values
    * Rows with strings "no port", "collapsed", "unknown", "unidentified"
    * Ports outside U.S as we are focussing only US ( state code > length(2) )

  * dim_uszips : Processed by pandas
    * Drop unused columns in df_uszips

  * dim_i94countrycode : Processed by pandas
    * Remove invalid codes / Collapsed / No Country
    * MEXICO text will be updated - MEXICO Air Sea, and Not Reported (I-94, no land           arrivals)

  * dim_usdp & dim_usdr : Processed by pandas
    * Convert floats to ints for columsn 'Male Population', 'Female Population', 'Number       of Veterans', 'Foreign-born', 'Total Population', 'Count'
    * Keeping only rows where MP + FP = TP
    * Checking if any cities are misspelled, if so correct spelling and sum up                 duplicates if they are in same city but divided by directions(ie., N/E/S/W)
    * Split main demographic table into two, i) based on population ii) based on race
    * Unmelting to converts Race rows to columns

  * dim_temper : Processed by pandas
    * Convert dt to datetime from object
    * drop columns AverageTemperatureUncertainty, Latitude, Longitude
    * Removing missing temperatures
    * Eliminating the duplicates(ie., multiple locations in same city)
    * Keeping only USA

After exploring, assessing & cleaning data, lots of questions arised and below are a few, i would like to get answers and produce a report,

1. Most tourists are from which country
2. Most visited state in US
3. How long do they stay in US
4. Travellers avg age graph
5. Tourism by months
6. States and their average temperature
7. Less diversed states

To speed up the cleansing process, initially all the datasets were cleaned using Pandas and later for i94 dataset alone Pandas code were converted to Pyspark code to perform cleaning efficiently on a bigger dataset. This process for quite challenging as i had to get similar results which i had got in Pandas.
Below are the Jupyter Notebooks used for the exercise :

Capstone Project - A - Pandas.ipynb - Primary
Capstone Project - B - Spark.ipynb - Contains code mostly related or required for i94 dataset processing
Capstone Project - C - Spark tables.ipynb - Did not go with this approach as queries were slow due to the massive scans on big datasets
Capstone Project - D - Analytics.ipynb - This notebook will query Redshift and generate graphs

## Step 3: Define the Data Model

## 3.1 Conceptual Data Model
After reviewing and cleaning all the datasets, it seems i94 data will be center of this data model.

## We may require two different types of tables,
   1. Staging tables : This can be used for preprocessing the data before loading into         the main Data Warehousing tables. Here will filter complex conditions which may be       tedious in Spark/Pandas.
   2. Data warehouse tables : This will be the main Fact & Dimension tables

![image](https://user-images.githubusercontent.com/90289879/198860716-740a71db-2c6e-43a3-9e58-1be491c12eda.png)


## 3.2 Mapping Out Data Pipelines

Lots of thoughts and efforts have been put in identifying valid, invalid, unknown data and lot of assumptions also been made in giving meaning to the data as well during assessment it had helped in discarding dirty rows. If it was a real system, that data wouldn't be discarded it would be moved into unclean data table to further analyze why the data was invalid and setup systems, steps & procedures in-place to reduce these kind of rows.

All the issues, thats been identified during "Step 2: Explore and Assess the Data" will be processed and issues will be handled in this stage by program process-df.py

process-df.py performs the following :
1. Reads all the CSV/JSON files found in ./inputs folder into pandas dataframe and processe them.
2. Reads i94 SAS datasets from directory ../../data/18-83510-I94-Data-2016/ one-by-one and process it using Pyspark and append the temporary processed dataframe to another spark dataframe.
3. All the final Panda dataframes are saved in CSV format in folder ./outputs/ and i94 spark dataframe is saved to ./outputs-gzip/dfs_ids1.gzip folder in zipped CSV format.

## Step 4: Run Pipelines to Model the Data

## 4.1 Create the data model
Using the final dataframe, we can create the relational data model.

Below are the Fact & Dimension tables, we will be using for this project
Fact table
  * fact_ids : Contain details on person entering US
  
### Dimension tables

    1. dim_visatype : This is a reference table contains information on US Visa type            code & description
    2. dim_ac : Contains information on US airports
    3. dim_USPoE : This is a reference table contains information on US Port of Entry          code, city & state names
    4. dim_uszips : This is a reference table contains information of all US city              details
    5. dim_i94countrycode : This is a reference table contains i94 country code & name
    6. dim_usdp : This table contains population details of state and cities in US.            Helps analyze to which cities people like to travel to for Business/Pleasure
    7. dim_usdr : This table contains racial information
    8. dim_temper : This table contains information on US temperature details by City,          helps analyzing at what season people like visiting
    9. dim_alc : This is a reference table contains information on Airliners
    10. dim_visapost : This is a reference table contains code & place name where the           visa was issued
    11. dim_alpha2countrycode : This is a reference table
    12. dim_iap : This is a reference table contains information on Airport name & IATA         code
    13. dim_wc : This is a reference table containing geographic details of world cities
    14. dim_USstatecode : This is a reference table contains information on a US state,         its two character code, status of the place whether its state or territory
    
    
By reviewing the dataframes, we can write DDL statements for the Fact & Dimensional tables. I have found an easy process (pd.io.sql.get_schema) to generate DDL statements from a dataframe.

![image](https://user-images.githubusercontent.com/90289879/198860845-2d0d345f-dc0c-4686-abf5-6ae138880f3e.png)


## 4.2 Data Quality Checks

Data Quality Check is performed at two stages,

1. In process-df.py, after processing the dataframes, assert statements are added to        validate if the dataframe has rows or not.
2. In etl.py, after loading, SQL statements to display counts are executed.

## 4.3 Data dictionary

Simple and straight forward table design is chosen which will help in building simple to complex queries for analytics. Some of the data fields name have been renamed to make it meaningful and easy to understand.

Click here to see the "Project Capstone - Data dictionary"


## 4.4 Analytics

Top visitors to US are from Mexico, China, Brazil and India.

![image](https://user-images.githubusercontent.com/90289879/198860864-d04d2d91-9e51-4ebb-89e3-055c58f1d642.png)

Most visited state in US seems to be Florida & California

![image](https://user-images.githubusercontent.com/90289879/198860869-f11f8e8d-1e03-451d-b904-6f6135423d36.png)

Florida is also sometimes referred to as the Sunshine State, and is a major tourist attraction due to its various beaches and parks. For example, Amelia Island is home to Fernandina Beach, which has unique features and a charming shrimping village that attracts vast numbers of travelers to the region. Other notable sites in Florida include the Castillo de San Marcos, Walt Disney World, the Everglades, South Beach, and the famous Overseas Highway, which connects the Florida Keys to the mainland.

California is the most visited state in the United States. The state's vast area is home to various vibrant cities, amusement parks, beaches and natural wonders that are popular among travelers from America and the rest of the world. Both Los Angeles and San Francisco are considered Gateway Cities, and are home to some of the most popular and recognizable tourist spots in the country. These include the Golden Gate Bridge, Disneyland, and Hollywood. Additionally, some of the national parks located in California are also significant tourist destinations. For example, Yosemite National Park in Northern California is well know for its tall mountains, valleys, and beautiful waterfalls.

Top reason to visit US seems to be for Pleasure
![image](https://user-images.githubusercontent.com/90289879/198860874-0a6bd8c6-7f0b-46a0-9406-1c3da50ac1e7.png)

Tourists by Country & Age range
![image](https://user-images.githubusercontent.com/90289879/198860887-dd537c78-6908-48b9-9131-f7ef3ce5c1ac.png)

This graphs gives a lot of valuable information like,

1. People of age between 40 - 55, travel a lot as that age range has the highest count      except india.
2. In Indian bars, people between age 55-70 travel to US more(To visit kids, after          retirement travel may be)

Visit purpose & Number of days stay in US

Student : Next to chinese are the indians

![image](https://user-images.githubusercontent.com/90289879/198860907-b8b4d22d-7972-4bf6-8e69-a1917538e1a2.png)

Business : In business again China tops, next is india, mexico and brazil
![image](https://user-images.githubusercontent.com/90289879/198860912-c5324f10-52b8-464b-b8a0-c2a1b8e08241.png)

Pleasure : Indian tend to stay longer in US when compare to other countries in the range beween 60-100 & 100-200
![image](https://user-images.githubusercontent.com/90289879/198860913-4124a4e2-41d9-4f3c-ba76-2251d4bab500.png)

Gender by county - There are more females in US than males. :D Interesting
![image](https://user-images.githubusercontent.com/90289879/198860917-c2e8203f-22cb-4b68-9ba9-5d810fdb08d4.png)

Major race in US is White
![image](https://user-images.githubusercontent.com/90289879/198860919-2cd4544f-6cbd-47d6-8d1c-c29d5acaf671.png)

People like visiting Us during holiday season and other than Feb every month around 800k visitors are entering US.
![image](https://user-images.githubusercontent.com/90289879/198860924-6807c0a4-2c22-4000-a791-8400bd28c897.png)

Weather map with Min, Avg & Max weather
![image](https://user-images.githubusercontent.com/90289879/198860929-f4f08b86-ace8-4d4f-9765-cad32622e84e.png)

Global warming is happening!
There is a continous trend of increasing temperature in the last 100+ years
![image](https://user-images.githubusercontent.com/90289879/198860933-e2c8d029-a975-4977-be2c-b05c74447957.png)

![image](https://user-images.githubusercontent.com/90289879/198860937-e70d1b03-e198-4b45-b51a-e5c9619d7569.png)

![image](https://user-images.githubusercontent.com/90289879/198860943-12bb1ff9-e837-4263-a589-ae761a3ed91f.png)

## Step 5: Complete Project Write Up

### Tools & Technologies

    * Python : Python is used as the programming language to perform the data analysis         as it supports wide variety of libraries to perform tasks faster and community in       Stack Overflow to help with any issues.
    * Pandas : Panda in Jupyter is used for initial data analysis.
    * Spark : Pyspark is used to process the Big data.
    * AWS S3 : S3 used for Storing processed outputs.
    * AWS Redshift : Redshift is used as warehousing database to perform query analysis.
    
Data updates

This being a Warehouse environment, all new data will be appended to the Fact & Dimension tables not replaced.

   * Weekly : Below tables hold static/reference data which can be updated once a week.
     1. dim_visatype
     2. dim_ac
     3. dim_USPoE
     4. dim_uszips
     5. dim_i94countrycode
     6. dim_alc
     7. dim_visapost
     8. dim_alpha2countrycode
     9. dim_iap
     10. dim_wc
     11. dim_USstatecode

   * Monthly : Temperature data is collected 1st of every month and demographic data can be updated once a month
     * dim_temper
     * dim_usdp
     * dim_usdr

## Alternative approach to solve problem under different scenarios

## The data was increased by 100x

To process 28Million rows by process-df.py takes around 9500-10k seconds thats around 2.5hrs in Udacity workspace. 100x more data probably would take 10x - 100x more time, thats lot of time. So approach would be, input data should be stored in S3 and data processing should be done in EMR. If the input data is zipped, it would save on S3 storage and Pyspark can read zip files. Redshift can handle 2.8 Billion rows easily as Single node of Redshift can handle of about 160GB and max 128 Compute nodes. Being a warehousing database of choice in AWS, it should handle it.

## The data populates a dashboard that must be updated on a daily basis by 7am every day

Scheduling can be setup based on answers following questions,

  1. How often data is updated ?
  2. How clean input data will be ?
  3. Will the input data be available on time ?
  
If the scheduling is complex and if scheduler should handle failures, retries, backfills, logical conditions to run a job, retreive data from multiple databases or sources, it is better to go with a familiar tool like Airflow. For other simplistic purposes, CRON will suit.

For this Capstone project, if data needs to be updated everyday to be presented in a dashboard, a simple cron job can be started at 3AM as the process-df.py could take around 3hours to complete and etl.py could take few minutes. It should complete before 7AM.

If the data is increased by 100x, then we would need Airflow to handle error, retries and start EMR on demand to process huge volume of data.

## The database needed to be accessed by 100+ people

Redshift can support upto 500 connections, so 100+ people can easily connect to Redshift.

But question should be, how much of data will be accessed by 100+ people ?

## Pricing Analysis

Redshift : $5.00 per terabyte of data scanned
S3 Pricing : First 50 TB / Month is $0.023 per GB, So 1TB = $23

Udacity Capstone Data Size = 6GB

Redshift Cost for 6GB = $0.03
Redshift Cost for 100x data = 600GB = $3
If 100+ people scan 100x(600GB) data once = 100 * 600GB = 60TB = $5 * 60TB = $300

S3 Cost for 6GB = $0.138
S3 Cost for 100x data = 600GB = $13.8
If 100+ people scan 100x data(600GB) once = 100 * 600GB = 60TB = $133.68
For above calculation AWS Pricing Calculator was used. S3 seems cheaper, but things can go pretty expensive quickly, if the environment is not controlled. So, users must be allowed to access only limited data. Volumes similar to production can be kept in UAT environment and development environment should have smaller subset of data.

## Issues faced

* .CRC files : While uploading files to s3, it creates these CRC files, so when i try to    run redshift COPY statement when those .CRC files are in the folder, COPY statement      would fail.
   Solution : Delete all those .CRC & _SUCCESS files from s3 bucket before running COPY    statement.

* Parquet files : Null fields will not be present in the parquet format, so COPY           statement will fail wiith below error. Found the fields are missing the in the parquet   format by using s3 "Select From" technique where in the s3 console you can select the   file and see a preview of that file.

    COPY staging_ids FROM 's3://sushanth-dend-capstone-files/i94-apr16.parquet/' IAM_ROLE 'arn:aws:iam::164084742828:role/dwhRole' FORMAT AS PARQUET ;
    
S3 Query Exception (Fetch)
DETAIL:  
  -----------------------------------------------
  error:  S3 Query Exception (Fetch)
  code:      15001
  context:   Task failed due to an internal error. Unmatched number of columns between table and file. Table columns: 23, Data columns: 22, File name: https://s3.us-west-2.amazonaws.com/sushanth-dend-capstone-files/i94-apr16.parquet/part-00000-6034cb60-860e-4a6c-a86d-6
  query:     1867
  location:  dory_util.cpp:1119
  process:   fetchtask_thread [pid=1331]
  -----------------------------------------------
Solution : Use CSV GZIP

* Plotly : Tried installing plotly for maps(Choropleth Maps in Python). Could get to run   in Udacity Workspace. Lots of errors. So, went for other simpler approach, but issue     with that approach is Legends it comes as a separate picture, but works.

## Lessons learned

   * Indentations : When copying code from jupyter to python file, need to take care of      indentations.
   * Assertions : When processing dataframe via functions(i., input raw dataframe,            output processed dataframe), check on all the factors you think that would make a        valid processed dataframe, simplest is checking the counts. So add assert after          retreiving dataframe from a function.
   * Redshift Uniqueness : Uniqueness, primary key, and foreign key constraints are          informational only; they are not enforced by Amazon Redshift.
   * Redshift COPY statement : Unload data column order in dataset should match with        * Redshift table columns order. Otherwise COPY statement should be like copy              venue(venueid, venuecity, venuestate) meaning unload column order is venueid,            venuecity, venuestate
   * Jupyter Graph Images : Sometimes cells don't refresh images even if the image is        changed. You will have to copy the code and paste it in another cell.
   
   
## Reference
   1. Distance Between Two Locations (Sphere) - https://simplemaps.com/resources/location-distance

   2. World Cities - https://www.nationsonline.org/oneworld/cities.htm

https://catalog.data.gov/dataset/us-ports-of-entry

   3. Passport Number - i94 - Excel template

      * Information from excel sheet
        * State code(char(2)) & State name
        * Country code(char(3)), country name, visa wavier indicator
        * Gender can be M/F/O
          * According to facebook there are 58 genders
   4. i94 travel/border crossing FAQs

   5. U.S Demographics 2015

   6. Advanced Choropleth Maps - Kepler.gl

   7. Weather Charts inspiration

   8. Weather data analysis

   9. fuzzywuzzy - Finding fuzzy duplicates

   10. Air Categories

      Click to see definition of Airport Categories

   11. List of states and territories of the United States
      US consists of 50 states, 48 contiguous states and Washington, D.C., are in North       America between Canada and Mexico, while Alaska is in the far northwestern part of       North America and Hawaii is an archipelago in the mid-Pacific.

      United States has sovereignty over 14 territories of which only 5 are                   inhabited(American Samoa, Guam, Northern Mariana Islands, Puerto Rico, US Virgin         Islands)

   13. Difference between ICA0 & IATA Codes ?

      * ICAO (International Civil Aviation Organization) is a UN-body which focusses on         international harmonization of civil aviation regulations. ICAO codes are used           for "official" purposes such as Air Traffic Control; E.g. flight plans use ICAO         codes for airports and airline flight identification.

      * IATA (International Air Transport Association) is a trade association that               focusses on making air traffic businesses safe, secure, reliable and efficient.         IATA codes are mainly used for ticketing. E.g. travel itineraries use IATA codes         for airports and IATA flight numbers.
