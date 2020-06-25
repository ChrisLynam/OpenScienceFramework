#### Connect to a PostgreSQL Database from R 

Load the required libraries from the CRAN 

```r 

library(RODBC)
library(RPostgres)
library(DBI)
```

Establish the connection introducing the database connection detail . 

1. **Driver:** If you have an ODBC defined driver in your computer  used here , otherwhise use *dbDriver("Postgres")*
2. **host:** The url/address of the PostgreSQL Database Server. If it is installed in your computer use: *localhost*
3. **port:** Define the port used by your PostgreSQL Database Server. Default used port is 5432.  
4. **dbname:** The name of the database within the PostgreSQL DB Server you want to connect 
5. **user:** Login user name 
6. **password:** Login password 

```r 
### GeoFISH Database Connection string ###

pw <- {"<your password>"}
# loads the PostgreSQL driver
drv <- dbDriver("Postgres")
# creates a connection to the postgres database. "con" parameter will be used in each connection to the database
con <- dbConnect(
              drv,
              dbname = "development_db",
              host = "azsclnxgis01.postgres.database.azure.com",
              port = 5432, 
              user = "<your user>", 
              password = pw ) #, options = 'ssl_mode=allow')

```
Using the function *dbGetQuery* you can send an SQL query to the database, and store the query result in a R variable. 

```r

locations <-  DBI::dbGetQuery(con, paste0( '
  SELECT *
  FROM  locations; ' 
) )
    
```
    
Be sure to terminate your connection once you finis querying the Database 

```r 
dbDisconnect(con)
dbUnloadDriver(drv)

```
