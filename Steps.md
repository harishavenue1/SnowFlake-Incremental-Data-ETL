# Create End To End Automated Snow Pipe to Load Incremental Data from S3

1) Create Table, File Format, and Staging Config to read files from Aws S3

-- table

    CREATE OR REPLACE TABLE employees (
        id INTEGER,
        first_name STRING,
        last_name STRING,
        email STRING,
        location STRING,
        department STRING
    )

-- file format

    CREATE OR REPLACE FILE FORMAT csv_file_format
        TYPE = 'CSV'
        FIELD_DELIMITER = ','
        SKIP_HEADER = 1
        FIELD_OPTIONALLY_ENCLOSED_BY = '"';

-- stage

    CREATE OR REPLACE STAGE my_stage_s3_init
        URL = "s3://snowflake-db-harish/employees"
        STORAGE_INTEGRATION = s3_init
        FILE_FORMAT = (FORMAT_NAME = 'csv_file_format');

-- stage validation [if any files available]

    LIST @my_stage_s3_init;

-- create pipe

    CREATE OR REPLACE pipe employee_pipe
    AUTO_INGEST = TRUE
    AS 
    COPY INTO employees
    FROM @my_stage_s3_init;

-- pipe properties to automate notification from S3 to Snowflake
  
    DESC pipe employee_pipe;

![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/50166cbf-aea7-427f-b07b-a5785d63e46c)

-- Copy notification details and Update to S3 under Bucket -> Properties -> Events

![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/73404df4-5387-4d8c-baa0-cbb5c0df1e25)

![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/a8106097-62ca-401f-8010-c6ced9cebfeb)

![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/e7efdf12-474a-4f35-871a-164e63b1568a)

-- Validate incremental data loaded via row count

    SELECT * FROM employees;

1st File 100 Records
![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/81422c58-0690-4c3f-b0ce-9d541df53bfe)
![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/bb4f487a-a915-4c61-9a12-a40b90b67d8f)

2nd File 200 Records
![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/31418af0-c4ca-45e3-99a2-bbe4ec4b7131)
![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/83e1da37-e673-4600-9542-274d282c3340)

3rd & 4th File Added = 400 Records
![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/a266fbc9-146d-4178-8dfb-5f21bfdd13c4)
![image](https://github.com/harishavenue1/SnowFlake-Incremental-Data-ETL/assets/21108205/0bd63631-a373-43ce-9466-28305e1900d1)

