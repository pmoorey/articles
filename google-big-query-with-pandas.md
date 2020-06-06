# Uploading Data to Google BigQuery using Python/Pandas

## The Goal
I had a requirement to populate a Google BigQuery table from a CSV file containing 1,000 rows of data for each day spanning several weeks.  To make it a bit trickier, I had to create an extra column containing the date.  This would require a lot of clicking within the BigQuery website, and editing of data within Microsoft Excel.  Instead it was automated with Python by using Pandas and Google BigQuery libraries.

## The Process

Here's the process, see if you can spot the optimization that I should have made!

1. Define the time range with the required start and end dates
2. Import CSV file as a Pandas data frame
3. While the start date is less than or equal to the end date:
   - Generate values (current start date) for each row in the new data column
   - Create the new date column and assign the values to each row
   - Upload the data frame to Google BigQuery
   - Increment the start date

I later realized the most efficient solution would be to append all data into a single data frame and upload it.  Nevertheless, the approach worked, albeit a bit slower than necessary.

### The Code

Requirements:
```pip3 install google-cloud-bigquery pyarrow pandas```

Script:
```
from google.cloud import bigquery
import pandas as pd
import os
import datetime


projectid = os.getenv('GOOGLE_PROJECT')
table_id = os.getenv('GOOGLE_BQ_TABLE')


def upload_to_bq(dataframe):
    ''' function to upload data to BigQuery '''
    client = bigquery.Client()
    job = client.load_table_from_dataframe(dataframe, table_id)
    job.result()
    print(f"uploading data to Google BigQuery is {job.state}")


if __name__ == '__main__':

    # define a range of dates
    start_date = datetime.date(2020, 6, 1)
    end_date = datetime.date(2020, 6, 12)
    delta = datetime.timedelta(days=1)

    # open CSV containing data to upload
    original_df = pd.read_csv('data.csv', encoding='utf-8')

    # iterate over each date in range
    while start_date <= end_date:

        print(f"processing dataframe for day {start_date}")

        # create copy of original data frame
        df = original_df

        # create an array containing data values to be assigned to rows in data frame
        values = [start_date for i in df.index]

        # create new 'date' column and assign values to each row
        df = df.assign(date=pd.Series(values))

        # upload dataframe to BigQuery
        upload_to_bq(df)

        # increment start date by one day
        start_date += delta

```
