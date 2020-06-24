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

## Files

```
main.py
```
This file is your entry point into the software. 
Set your download path which tells [Selenium](https://selenium-python.readthedocs.io/) where to save the 'Popular Products' CSV from Merchant Center.
Set your final path. This is where the CSV will be moved to once processing is complete
## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)
