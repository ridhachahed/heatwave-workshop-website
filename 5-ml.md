---
title: 5-ML
nav: true
---
# Machine Learning with Heatwave AutoML and Lakehouse


## Overview
This lab is designed to guide you through the process of loading unstructured data into a Lakehouse table using MySQL and leveraging this data for machine learning applications. We assume that you have already uploaded your unstructured data to an object storage and have a Pre-Authenticated Request (PAR) URL to access this data.

## Objectives:
- Learn to create an external table in MySQL that references unstructured data in object storage.
- Understand how to query unstructured data from a Lakehouse table.
- Explore machine learning concepts and apply them to your unstructured dataset.
- Explain the predicitons of your trained model.

## Prerequisites:
- Active MySQL shell connected to your database system.
- Unstructured data already uploaded to object storage with a valid PAR URL.
- Basic understanding of SQL queries and familiarity with the MySQL environment.


## Part 1: Setting Up Your Lakehouse Table


LakeHouse tables allow you to query data stored in external storage systems as if it were inside your MySQL database. They are especially useful for managing large, unstructured datasets without importing them directly into your database, offering flexibility and efficiency.

Creating a lakehouse table does not involve defining a schema that reflects the data you want to query. Indeed, using the stored procedure heatwave_load will infer 
the schema from the data and create a table. 

To locate the data file, a PAR (Pre-Authenticated Request) URL is used to securely access the data in the object storage, ensuring that data access is controlled and secure.

Here is below an example of how to use heatwave_load to load the unstructed banking data we have in our bucket. All you have to set is the correct **<PARL_URL>**:
``` 
CREATE DATABASE ml_data;

USE ml_data; 

SET @db_list = '["ml_data"]';

SET @ext_tables = '[
{
  "db_name": "ml_data",
  "tables": [{
    "table_name": "bank_marketing",
    "dialect": {
        "format": "csv",
         "field_delimiter": ";",
          "record_delimiter": "\\n",
          "has_header": true,
          "skip_rows": 0
      },
    "file": [{
      "par": "<PARL_URL>"
        }]
   }]
}
]';

SET @options = JSON_OBJECT('external_tables', CAST(@ext_tables AS JSON));

CALL sys.heatwave_load(@db_list, @options);

DESCRIBE ml_data.bank_marketing;
``` 

Once the data is loaded, you can Heatwave AutoML to train a model on your loaded data. 

``` 
-- Train the model
CALL sys.ML_TRAIN('ml_data.bank_marketing', 'y', JSON_OBJECT('task', 'classification'), @model_bank);

-- Load the model into HeatWave
CALL sys.ML_MODEL_LOAD(@model_bank, NULL);

-- Score the model on the test data
CALL sys.ML_SCORE('ml_data.bank_marketing', 'y', @model_bank, 'accurarcy', @score_bank, null);

-- Print the score
SELECT @score_bank;
``` 

``` 
CREATE TABLE bank_marketing_test
        AS SELECT * from bank_marketing_test LIMIT 20;
    
ALTER TABLE bank_marketing_test SECONDARY_LOAD;

``` 

With the trained model, you can do some predictions and even explain the predictions and the model.

``` 
CALL sys.ML_PREDICT_TABLE('ml_data.bank_marketing_test', @bank_model, 
        'ml_data.bank_marketing_predictions', NULL);

CALL sys.ML_EXPLAIN('ml_data.bank_marketing_test', 'y', @bank_model, 
        JSON_OBJECT('prediction_explainer', 'permutation_importance'));

CALL sys.ML_EXPLAIN_TABLE('ml_data.bank_marketing_test', @bank_model, 
        'ml_data.bank_marketing_lakehouse_test_explanations', 
        JSON_OBJECT('prediction_explainer', 'permutation_importance'));
``` 


``` 
SET @row_input = JSON_OBJECT(
    'age', bank_marketing_test.age,
    'job', bank_marketing_test.job,
    'marital', bank_marketing_test.marital,
    'education', bank_marketing_test.education,
    'default1', bank_marketing_test.default1,
    'balance', bank_marketing_test.balance,
    'housing', bank_marketing_test.housing,
    'loan', bank_marketing_test.loan,
    'contact', bank_marketing_test.contact,
    'day', bank_marketing_test.day,
    'month', bank_marketing_test.month,
    'duration', bank_marketing_test.duration,
    'campaign', bank_marketing_test.campaign,
    'pdays', bank_marketing_test.pdays,
    'previous', bank_marketing_test.previous,
    'poutcome', bank_marketing_test.poutcome);

SELECT sys.ML_PREDICT_ROW(@row_input, @bank_model, NULL)
    FROM bank_marketing_test LIMIT 4;

SELECT sys.ML_EXPLAIN_ROW(@row_input, @bank_model,
        JSON_OBJECT('prediction_explainer', 'permutation_importance'))
        FROM bank_marketing_test LIMIT 4;
``` 
