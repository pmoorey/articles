# Enriching Excel Files with Python Pandas

I was asked by one of my colleagues to discover the locations of ~2,000 endpoints in a global enterprise network.  They provided a detailed Excel document which included the IPv4 address of each endpoint.  To translate the IP addresses to locations I used a web service/API which accepted an IP address as input and responded with the location.  I'm not aware of a way to make API calls natively with Excel, perhaps it can be done with TypeScript in Excel, I'm not sure. Anyway, I used Python and 'pandas' data analysis library to update the Excel document with each individual location automatically.  

The script took 12 minutes to write, performing the task manually wouldn't have been possible, given the effort required.

To learn about <a href="https://pandas.pydata.org/" target="_blank">pandas</a> I recommend the <a href="https://pandas.pydata.org/docs/index.html" target="_blank">official documentation</a>, it's well written and easy to understand. 


![Image of Yaktocat](https://miro.medium.com/max/700/1*fdZiokZzHRCkoMLgrn0tpw.jpeg)
<br>*Photo courtesy of https://www.chinadaily.com.cn/*

## Preparation

First, you'll need Python and Python PIP (package manager).  You can get python from <a href="https://www.python.org/downloads/" target="_blank">here</a>.  Instructions for installing Python PIP are available <a href="https://pip.pypa.io/en/stable/installing/" target="_blank">here</a>.  Next, create a requirements file, we'll need a few Python modules that are not built-in:

*requirements.txt*
```
requests
pandas
xlrd
openpyxl
```

Install the modules using Python PIP:

```pip install -r requirements.txt```

## The Script

High level process:

- Open Excel file as Pandas data frame
- Get the data from the IPv4 Address column
- Loop over each IP address and call the web service to get the location
- Store the locations in a list
- Create a new column in the data frame and assign the location values
- Save the data frame as a new file

Here's the Python script, hopefully you can follow the comments to understand the flow.  

*excel-enrichment.py*
```
import requests
import pandas as pd
import logging

# setup logging, so we can view what's happening as it executes
logging.getLogger().addHandler(logging.StreamHandler())
logging.getLogger().setLevel(logging.INFO)

# define the file/sheet names
existing_file = 'endpoint-report.xlsx'
new_file = 'endpoint-report-with-locations.xlsx'
sheet_name = 'Endpoint Data'


# reusable function to get the location for a given IP address
def make_api_call(ip_address: str):
    """Return the location for a given IP address."""
    url = f"https://mywebsite/api/?ip={ip_address}"
    resp = requests.get(url=url)
    if resp.status_code == 200:
        location = resp.json()['location']
        return location
    else:
        raise HTTPError(resp.status_code, resp.text)


if __name__ == '__main__':

    # open the Excel document and import as a Pandas data frame
    df = pd.read_excel(existing_file, sheet_name=sheet_name)

    # get the values of the column 'IPv4 Address'
    ip_addresses = df['IPv4 Address']

    # create an empty list to store the location values
    locations = []

    # get the total rows in the data frame and create a counter to track progress
    total_ips = len(ip_addresses)
    processed_ips = 0

    # loop over each IP address
    for ip in ip_addresses:
        # query API to get the location
        location = make_api_call(ip)

        # log to screen the IP and the discovered location
        logging.info(f"{processed_ips}/{total_ips}: {ip} --> {location}")

        # add the discovered location to the list of location values
        locations.append(location)

        # increment the number of processed IP addresses
        processed_ips += 1

    # create a new column in the dataframe called 'location' and assign the values from the now populated list
    df['location'] = locations

    # save the data frame to a new Excel document
    df.to_excel(new_file, sheet_name=sheet_name)

```

Whilst it executes you will see output like the example below, and a new Excel document will be created in the same folder as your script:

```
12/1798: 10.200.4.202 --> mylocation1
13/1798: 10.200.4.204 --> mylocation1
14/1798: 10.200.4.206 --> mylocation6
15/1798: 10.100.99.201 --> mylocation9
16/1798: 10.200.5.205 --> mylocation8
17/1798: 10.200.4.198 --> mylocation1
18/1798: 10.200.4.199 --> mylocation1
```

I hope this was useful!
