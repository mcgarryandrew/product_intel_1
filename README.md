## Set-Up

Python Version 3.7 was used.

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install the requirements.

```bash
pip3 install -r requirements.txt
```

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

## Usage

```python
import foobar

foobar.pluralize('word') # returns 'words'
foobar.pluralize('goose') # returns 'geese'
foobar.singularize('phenomena') # returns 'phenomenon'
```

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)
