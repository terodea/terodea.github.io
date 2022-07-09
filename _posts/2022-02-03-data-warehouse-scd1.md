---
layout: post
title:  "Data Warehouse: Slowly Changing Dimesions - Type 1 in Apache Hive"
date:   2022-02-03 18:34:10 +0700
categories: [bigdata, hive, data-warehouse]
---

# About: What is Slowly changing dimension ?
A Slowly Changing Dimension (SCD) is a dimension that stores and manages both current and historical data over time in a data warehouse. It is considered and implemented as one of the most critical ETL tasks in tracking the history of dimension records.


## Type 1 SCDs - Overwriting/ Upsert(If new_record THEN Insert ELSE UPDATE)
In a Type 1 SCD the new data overwrites the existing data. Thus the existing data is lost as it is not stored anywhere else. This is the default type of dimension you create. You do not need to specify any additional information to create a Type 1 SCD. </br>

## Implementing SCD Type1 is a 4 step process: </br>
0. Initial step is an assumption that initial data alrady exists in table.
```SQL
DROP DATABASE IF EXISTS my_db cascade;
CREATE DATABASE my_db;
USE my_db;
-- Create an external table pointing to our initial data load (1000 records)
CREATE EXTERNAL TABLE contacts_initial_stage(
        id int, 
        name string, 
        email string, 
        state string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' STORED AS TEXTFILE
    LOCATION '/tmp/merge_data/initial_stage';
```
1. Create the target sink **managed** table in Hive
```SQL
-- Create the Hive managed table for our contacts.
CREATE TABLE contacts_target(
        id INT, 
        name STRING, 
        email STRING, 
        state STRING
    )
    CLUSTERED BY (id) INTO 2 BUCKETS STORED AS ORC TBLPROPERTIES("transactional"="true");
```
2. Insert initial data load into the new table

```SQL
-- Copy the initial load into the managed table.
INSERT INTO contacts_target SELECT * FROM contacts_initial_stage;
```

3. Create new **external** table pointing to new data load.
```SQL
-- Create an external table pointing to our refreshed data load (1100 records)
CREATE EXTERNAL TABLE contacts_update_stage(
        id INT, 
        name STRING, 
        email STRING, 
        state STRING
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' STORED AS TEXTFILE
    LOCATION '/tmp/merge_data/update_stage';
```

4. Write the final Upsert(Insert or Update) Query.
```SQL
-- Perform the Type 1 Update (full table upsert)
MERGE INTO
  contacts_target
USING
  contacts_update_stage AS stage
ON
  stage.id = contacts_target.id
WHEN MATCHED THEN
  UPDATE SET name = stage.name, email = stage.email, state = stage.state
WHEN NOT MATCHED THEN
  INSERT VALUES (stage.id, stage.name, stage.email, stage.state);
```

5. Validate your SCD1 implementation
```SQL
-- Confirm we now have 1100 records.
SELECT COUNT(1) FROM contacts_target;
```

#### Pros:
1. Easy and fast to update.
2. Data Volumne is less as compared to other SCD's.
3. Maintance is less in comparision to other SCD's.
4. Simple implementation.
#### Cons:
1. Historical Data is lossed as history is not maintaned rather it is updated. However one can preserve historical data using backup data processes.
2. Not suitable for transactions.
3. Records which are not updated any values are also re-inserted.


**SQL Code Credits : [LINK](https://github.com/cartershanklin/hive-scd-examples)**
