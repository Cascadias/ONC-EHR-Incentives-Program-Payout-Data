A repo for working with data from [ONC's EHR Incentive Programs Data and Program Reports page.](http://www.cms.gov/Regulations-and-Guidance/Legislation/EHRIncentivePrograms/DataAndReports.html) after a tweet from [@Cascadia](https://twitter.com/cascadia/status/307973508833615873) and subsequent chat through direct messages inspired me to do something with the data.

Notes
-----
* All files in this repo's `data` directory are from [ONC's EHR Incentive Programs Data and Program Reports page](http://www.cms.gov/Regulations-and-Guidance/Legislation/EHRIncentivePrograms/DataAndReports.html). They are included only for convenience to fellow developers looking to get up and running with a copy of the data.
* Using [Data Science Toolkit](http://www.datasciencetoolkit.org/) for geocoding provider addresses but started getting 500 Internal Server Errors when using public DSTK host so I brought up my own instance (m1.medium) on Amazon EC2. 


Procedure
---------
1. **New mongo collection for hospitals (2,000 rows)**
  1. Import CSV from ONC into MongoDB: `mongoimport --type csv -d onc -c ProvidersPaidByEHRProgram_Dec2012_HOSP_FINAL --headerline --file data/ProvidersPaidByEHRProgram_Dec2012_HOSP_FINAL.csv`
  2. Added 2d geospatial index: `mongo onc --eval "db.ProvidersPaidByEHRProgram_Dec2012_HOSP_FINAL.ensureIndex({'geo.data.geometry.location':'2d'})"`
  3. Geocoded locations using `ruby geocode.rb`
  4. Exported relevant information to CSV using `mongoexport --csv -d onc -c ProvidersPaidByEHRProgram_Dec2012_HOSP_FINAL -o data/ProvidersPaidByEHRProgram_Dec2012_HOSP_FINAL-geocoded.csv -f "PROVIDER NPI,PROVIDER CCN,PROVIDER - ORG NAME,PROVIDER STATE,PROVIDER CITY,PROVIDER  ADDRESS,PROVIDER ZIP 5 CD,PROVIDER ZIP 4 CD,PROVIDER PHONE NUM,PROVIDER PHONE EXT,PROGRAM YEAR 2011,PROGRAM YEAR 2012,geo.provider,geo.updated_at,geo.data.types.0,geo.data.geometry.location.lat,geo.data.geometry.location.lng,geo.data.formatted_address"`

2. **New mongo collection for eligible providers (~106,000 rows)**
  1. Import CSV from ONC into MongoDB: `mongoimport --type csv -d onc -c ProvidersPaidByEHRProgram_Dec2012_EP_FINAL --headerline --file data/ProvidersPaidByEHRProgram_Dec2012_EP_FINAL.csv`
  2. Added 2d geospatial index: `mongo onc --eval "db.ProvidersPaidByEHRProgram_Dec2012_EP_FINAL.ensureIndex({'geo.data.geometry.location':'2d'})"`
  3. Geocoded locations using `ruby geocode.rb`
  4. Exported relevant information to CSV using `mongoexport --csv -d onc -c ProvidersPaidByEHRProgram_Dec2012_EP_FINAL -o data/ProvidersPaidByEHRProgram_Dec2012_EP_FINAL-geocoded.csv -f "PROVIDER NPI,PROVIDER STATE,PROVIDER CITY,PROVIDER  ADDRESS,PROVIDER ZIP 5 CD,PROVIDER ZIP 4 CD,PROVIDER PHONE NUM,PROVIDER PHONE EXT,PROGRAM YEAR,geo.provider,geo.updated_at,geo.data.types.0,geo.data.geometry.location.lat,geo.data.geometry.location.lng,geo.data.formatted_address"`

