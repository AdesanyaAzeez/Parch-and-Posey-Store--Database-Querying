# Parch and Posey Paper Store Analysis
Extracting data from a database using PostgreSQL, analyzing and visualizing in Looker Studio.
## Project Basis
This project is carried out to demonstrate the E-T-L process in analysis. The database holds various order information about a paper store, Parch & Posey. The documentation to the project include:
- Database Querying
- Data Loading and Transformation
- Data Modeling
- Data Visualization
- Limitation(s)
- Conclusion

## Database Querying
I used postgreSQL to interact with the database on *pgadmin* (an open-source tool for a postgreSQL database). The data to be used for this analysis spans across multiple tables. There are several tables in the database as seen on the E-R-D (entity relationship diagram) below, and I used JOINS to merge the relevant data into 2 different tables for analysis; **Orders Tables and Accounts Table**

![parchandposey E-R-D - Copy](https://github.com/AdesanyaAzeez/Parch-and-Posey-Store--Database-Querying/assets/95116501/bbf3663f-1141-4060-b83c-b36b3ab0e0c6)

**The Orders table** holds data about the metrics of the paper store, it contains information about the order date, units ordered, amount and more. I used the script below to query the database.

```
SELECT id order_id, 
      account_id, 
      occurred_at::DATE AS order_date, //format the order date column as date only(instead of a time stamp)
      UNNEST(array['standard_qty', 'gloss_qty','poster_qty']) AS paper_type, //unpivot the paper type column to fit its equivalent quantity row
      UNNEST(array[standard_qty, gloss_qty, poster_qty]) AS units_ordered, //unpivot the quantity columns into a row
      UNNEST(array[standard_amt_usd,gloss_amt_usd, poster_amt_usd]) AS total_amount // unpivot the amount column for papertype column
FROM orders;
```
NB: In restructuring the data to make it understandable and healthy for analysis and visualization, I used the UNNEST statement (a function used to expand an array to a set of rows) to unpivot the data. I made a post about unpivoting with UNNEST on [LinkedIn](https://www.linkedin.com/posts/abdulazeezadesanya_unpivot-in-postgresql-with-unnest-and-array-activity-7044353462251196416-5Ubq?utm_source=share&utm_medium=member_desktop) 

**The Accounts table** is a dimension-like table that holds further details about the orders. Multiple tables were joined together to extract the needed data.
Here is the script
```
SELECT ord.account_id,
      acc.name account_name,
      acc.website website,
      srp.name sales_rep,
      reg.name region
FROM orders ord JOIN accounts acc
  ON ord.account_id = acc.id
JOIN sales_reps srp
  ON acc.sales_rep_id = srp.id
JOIN region reg
  ON srp.region_id = reg.id;
```

## Data Loading and Transformation
The data tables from the sql script were downloaded as .csv files and loaded into Google Sheets. Here, I prepared the data further, cleaning and making sure the data is free from any errors. The transformation process involves 
- Renaming the columns and table using proper casing
- Assigning correct data types to each column
- Used text functions to extract irrelevant data from rows
- Filtering out rows with zero '0' value
- Removing duplicated rows that occured due to join

The data table were further loaded into Looker Studio.

## Data Modeling
I used the 'Data Blend' feature on Looker Studio to build my data model. This feature uses a JOIN-like configuration to create a relationship between data tables. I used the account_id as the KEY.
</br>In addition to the modeling, I created the ***Year*** and ***Month*** measures using the *order_date* column.

## Data Visualization
To gain better and clear insights of the data, I created a report in Looker Studio
</br>[Interact with the Report](https://lookerstudio.google.com/s/jspIDRFERSE)

<img width="1000" alt="Parch and Posey Orders Report" src="https://github.com/AdesanyaAzeez/Parch-and-Posey-Store--Database-Querying/assets/95116501/10b0301c-4b34-4ea1-b7d7-a630f895a579">

## Conclusion
This was a fun project to work on and I was able to learn more about queries and get a near real-life experience interacting with a database. Not all data included in the database were used for this analysis. Only needed data were extracted.

For further review, you can check out the [data tables](https://docs.google.com/spreadsheets/d/1vy4d-9eCBpZ1_qBUtwJ46X1YVF3ZbeuRRkSJ_ZMAeQ0/edit?usp=sharing)

For comments, observations or recommendations, feel free to connect with me on [LinkedIn](https://www.linkedin.com/in/abdulazeezadesanya) and [Twitter](https://twitter.com/Adesanya_AZ)

Documentation By: AbdulAzeez Adesanya

