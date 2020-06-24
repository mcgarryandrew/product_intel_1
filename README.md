NOTE: This is development code. There are a lot of efficiency and good practice improvements to be made. Feel free to improve upon it!

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
- Add week numbers to the data so we can identify which week the specific row of data belongs to. As the data is being looked at weekly, the daily date column was removed for grouping purposes. We mention this in the code's comments.
-Write the results to BigQuery

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)
