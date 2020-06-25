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

More advance and best practice R code to interrogate the DATABASE


### SELECT DATA FROM POSTGRESQL

```r 

### Fetch data from PostgreSQL Database 

  query_gt <- SQL(sprintf('SELECT * FROM  locations;'))

  locations <-  DBI::dbGetQuery(con,  query_gt )
  
  head(locations)
  str (locations)
  plot(locations)
```  
  ### SELECT SPATIAL DATA FROM POSTGRESQL

```r 
  
  ### spatial data must be read with Simople Features package (SF) 
  
  locations_sf <-  st_read(dsn = con,  query = query_gt)
  
  head(locations_sf)
  str (locations_sf)
  plot(locations_sf)
 
  
 ```
 
 #### Select data including R variables in SQL String
 
 ```r
  
  ## select data including R variables
  
  place <- 'Stokeworth'
  
  query_gt1 <- SQL(sprintf("SELECT * FROM locations WHERE name = '%s';", place ))
  
  locations_sf <-  st_read(dsn = con,  query = query_gt1)
  
  head(locations_sf)
  str (locations_sf) 
  
  
 ```
 
 #### Send queries to PostgreSQL
 
 ```r
 
  query_gt <- SQL(sprintf('create table locations_roi as select * from locations;'))
  DBI::dbSendQuery(con,  query_gt )
  
  query_gt <- SQL(sprintf('select * from locations_roi;'))
  
  locations_roi <-  DBI::dbGetQuery(con,  query_gt )
  

```
 
 #### Send DATA to PostgreSQL
 
 ```r

 ### Send DATA  to PostgreSQL Database 
  
  gid_ <- 99999
  name_ <- 'Vigo'
  type_ <- 'City'
  lon_ <- -8
  lat_ <- 42
  
  
  query_gt <- SQL(sprintf("INSERT INTO locations_roi (gid, name, type, lon, lat )  VALUES (%i, '%s', '%s', %d, %d ) ;", gid_, name_, type_, lon_, lat_))
  
  DBI::dbSendQuery(con,  query_gt )
  
  query_gt <- SQL(sprintf('select * from locations_roi;'))
  
  locations_roi <-  DBI::dbGetQuery(con,  query_gt )
  
  
  locations_roi %>% filter(name == 'Vigo')
  
  head(locations_roi)
  str (locations_roi)
  plot(locations_roi)
 
```

## TERMINATE YOUR CONNECTION

Be sure to terminate your connection once you finis querying the Database 

```r 
dbDisconnect(con)
dbUnloadDriver(drv)

```
