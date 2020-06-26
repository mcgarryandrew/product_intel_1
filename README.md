NOTE: This is development code. There are a lot of efficiency and good practice improvements to be made. Feel free to improve upon it!
NOTE: When refering to functions or methods, we mean the same thing.
## Set-Up

Python Version 3.7 was used.

Set up a virtual environment

```bash
pip3 install virtualenv
```
Specify a path for the Virtual Environment within your workspace
```bash
virtualenv env
```
Activate in Windows
```bash
env\Scripts\activate
```
Activate in Linux
```bash
source env/bin/activate
```

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install the requirements.

```bash
pip3 install -r requirements.txt
```

## Entry Point and Environment
```
env.py
```
Environment file which holds all your sensitive information.

```
main.py
```
This file is your entry point into the software. 

Set your download path which tells [Selenium](https://selenium-python.readthedocs.io/) where to save the 'Popular Products' CSV from Merchant Center.

Set your final path. This is where the CSV will be moved to once processing is complete

## Merchant Center Download Automation
```
csv_downloader.py
```
This file sets the parameters for the chromedriver. Selenium will use this to automate the download process for the CSV.

Make sure you're phone authenticated in chrome on the machine you plan to use before running the secript.

Put your merchant Center login information in the env.py file for this to work.

This CSV will be refered to as the ***Popular Products File*** from here onwards.

## Matching Products
```
match_maker.py
```
This is the file that does most of the work using SQL and Pandas.
- Get the required data from the product feed (this code assumes the feed is in XML format)
- Pull data from Google Ads and Google Analytics
- Apply any required filters
- Match GTINs from the Popular Products File onto the Adwords and Analytics data. These can be perfect or partial matches. We have done partial matching using SQL. Adjust the code accordingly:
```python
    sql = '''
    SELECT linkingTable.GID, linkingTable.Impressions, linkingTable.Cost, linkingTable.Clicks, linkingTable.ConversionValue, linkingTable.IMG, linkingTable.BRAND, linkingTable.monthNum, linkingTable.yearNum, dfPopM.Pop
    FROM linkingTable, dfPopM
    WHERE SUBSTR(linkingTable.GTIN,0,LENGTH(linkingTable.GTIN) - 1) = SUBSTR(dfPopM.GTIN,0,LENGTH(dfPopM.GTIN) - 3)'''
```
- Convert currency if required
- Work out specific metrics and KPIs e.g. Derive ROAS from Cost and Revenue instead of pulling ROAS from the API (if you can).
- Add week numbers to the data so we can identify which week the specific row of data belongs to. As the data is being looked at ***weekly***, the daily date column was removed effective SQL grouping. We mention this in the code's comments.
-Write the results to BigQuery

***Several other files are called from match_maker.py***

```
process_xml.py
adwords_pull.py
analytics_pull.py
csv_parser.py
```

***Process_xml:***
This file is where the data feed is processed. We use ***urllib*** to get the URL of the live data feed. However, you can eaily pass a local file into the file by changing the line:
```
xmldoc = ET.parse(url)
```
TO
```
xmldoc = ET.parse('dir/to/local/file.csv')
```
We have taken namspace into consideration as mentioned in the comments. Namespace may vary depending on the product data feed.
 
The main method takes several optional boolean parameters: **margins**, **decline** and **size**.

These parameters specify which file is calling the function. E.g If the file concerning product margins is calling the method then **margins** will be passed as ***True***.

If nothing is passed or all parameters are False ***match_maker.py*** is assumed to be calling the method by default. 

***adwords_pull:***
Pretty simple file. This just holds the API requests. The **get_raw_report** method is used for everything except the product_decline.py file. 

Product decline utilises the **ProductDecline** method.

The API responses are passed to ***csv_parser.py*** alongside a parameter **'ANL'** (analytics) or **'ADW'** (Adwords) identifying which API response needs to be processed.

You can adjust the metrics being pulled form the API, ***just make sure you refelct these changes in the csv_parser.py file.***

***adwords_pull:***
This file is similar to adowrds_pull.py. This simply pulls from the Analytics API and processes the response into CSV format.

There are API pulling methods: **get_report** and **get_report_sizes**

The first one is used for all files except the product_sizes.py, which is where get_report_sizes is used. Additionally, these API requests use segements which are completely optional depending on your use case.


## Product Margins and Product Sizes
These files are pretty similar. Product margin looks at the sales volume and ROAS of each margin (percentage discount). The data is processed using SQL and passed into BigQuery.
Product Sizes looks at the sales volume and ROAS for each size of shoe. This processes data in a similar way to product margins, but with a little more SQL.

Product sizes require a little string manipulation because of the formatting of some of the sizes - everything needed to be consistent. You can just adjust this accordingly to fit your needs.

## Product Decline
This file gets it's own section as it works a little bit differently to the others. This looks at the decline of a product over a 4 week period. The rule we used was: ***If the product has more than 400 clicks and has declined more than 50% in the last week (of the 4 week period) vs the first week*** then add it to the list of declining products.

This works by pulling each individual day of the 4 week period from Analytics (to avoid data sampling). Next, we match the Analytics and Adwords data together with some SQL.
We then loop through the dataframe and make sure each unique product group ID has data for the 4 week period - if not, then remove it from the dataframe. We use this loop to check if the last week of the period has declined more than 50% vs the first week.

Whatever is left in the dataframe are the products that fit the rule mentioned above. These are then sent to BigQuery.


## License
[MIT](https://choosealicense.com/licenses/mit/)
