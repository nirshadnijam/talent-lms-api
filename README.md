# talent-lms-api
This repository will contain code that extracts data from Talent LMS API. Transforms the extracted data and loads them into the database.

### Introduction
There are three notebooks that are responsible for hitting the TalentLMS API. The data is then loaded into memory. The data is undergoes some transformation and gets loaded into our MySQL database. The purpose behind this requirement was to automate the data flow from TalentLMS to our datawarehouse. There are few things to consider when we are accessing the TalentLMS API. This has some restrictions:
- We can only make 10,000 API call for an endpoint for a given hour
- We can only only 200 API calls every 5 seconds 

Even with these requirements, we are not running out quota. 

## What does the notebook responsible for loading users and courses do?
- When this notebook is triggered. This obtains are the user or course data from their (TalentLMS) server and loads them into memory. Once these are loaded, we do some transformation of the data. The transformation includes changing certain string field types into datetime. We also remove certain columns. This whole process is encapsulated under this funcation call: `load_lms_users()` or `load_lms_courses()`
- The next function that is called: `get_latest_df(df, table_name)`. This function does two things. One, it gets latest `updated_at` date from the MySQL users or courses table. Then, it filters outs for the users whose `updated_at` is greater than the obtained value. 
- The next function that is called: `create_temp_users_table(temp_df)` or `create_temp_users_table(temp_df)`. This creates temporary table on MySQL with the records that have been updated latest. 
- The last function call does an upsert operation using the temporary table against the actual table on the database. This ensures the data changes are updated and inserted accordingly.


