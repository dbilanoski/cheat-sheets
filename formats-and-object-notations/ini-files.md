INI files are simple text files organized into **Sections** and **Key-Value pairs**.

## Anatomy

```Ini
; Comments often start with a semicolon (or sometimes #)

[MailConfiguration]          ; <--- SECTION
Address=smtp.example.com     ; <--- KEY = VALUE
Port=587
Recipients=me@test.com, you@test.com  ; Lists are just strings we split later

[loader]
local_server=SQL2019
local_database=DataWarehouse
sql_path=./scripts/campaigns

[gc]
template_extrafields_uuid=123-abc-456
bulk_loading_rows_limit=1000
```


## Parsing INI Files With Python


```python
import configparser

config = configparser.ConfigParser()
config.read('config.ini')

# Accessing values
server = config['loader']['local_server']  # Returns "SQL2019"
port = config.getint('MailConfiguration', 'Port') # Returns integer 587
non_existant_data = config.getint('MailConfiguration', 'non_existing_key') # Throws a KeyError since the key does not existio
```