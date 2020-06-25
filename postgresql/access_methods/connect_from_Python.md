
#### Connect to a PostgreSQL Database from Python

Import the required libraries 

```python 
import psycopg2
import geopandas as gpd

```

Establish the connection introducing the database connection detail . 

1. **Driver:** If you have an ODBC defined driver in your computer  used here , otherwhise use *dbDriver("Postgres")*
2. **host:** The url/address of the PostgreSQL Database Server. If it is installed in your computer use: *localhost*
3. **port:** Define the port used by your PostgreSQL Database Server. Default used port is 5432.  
4. **dbname:** The name of the database within the PostgreSQL DB Server you want to connect 
5. **user:** Login user name 
6. **password:** Login password 

```python 

try:
    con = psycopg2.connect("dbname='development_db' user='editors_dev@azsclnxgis01' host='azsclnxgis01.postgres.database.azure.com' password='Dev!c5374'")
except:
    print("I am unable to connect to the database")
```
### CREATE A CURSOR TO THE DATABASE

```python 

# Create database connection cursor  
cur = con.cursor()

```
### FETCH DATA FROM THE DATABASE

```python 

query_sql = "select name, type , geom from locations"

df = gpd.GeoDataFrame.from_postgis(query_sql, conn, geom_col='geom' )

df.head()

df.plot();

```

### SEND QUERIES TO THE DATABASE
You can use now the cursors to explore and query the database and import tables into Python enviroment 


```python

cur.execute('Select * FROM locations' )
column_names = [data_desc[0] for data_desc in cur.description]

    
```


## TERMINATE YOUR CONNECTION

Be sure to terminate your connection once you finis querying the Database 

```r 
cur.close();
con.close ();

```
