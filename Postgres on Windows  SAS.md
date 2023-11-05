## sas ##
Log into the windows server.

The properties for the service "SAS [Config-Lev1] Web Infrastructure Platform Data Server" shows this:
```
"C:\Program Files\SASHome\SASWebInfrastructurePlatformDataServer\9.4\bin\pg_ctl.exe" runservice -N "SAS [Config-Lev1] Web Infrastructure Platform Data Server" -D "C:\SAS\Config\Lev1\WebInfrastructurePlatformDataServer\data" -w -o "-i -p 9432"
```

Open a CMD and change to 
CD C:\Program Files\SASHome\SASWebInfrastructurePlatformDataServer\9.4\bin\
run psql -p 9432 -U dbmsowner -d postgres
enter password


```
SELECT datname FROM pg_database;


SELECT usename AS role_name,
  CASE
    WHEN usesuper AND usecreatedb THEN
  CAST('superuser, create database' AS pg_catalog.text)
    WHEN usesuper THEN
  CAST('superuser' AS pg_catalog.text)
    WHEN usecreatedb THEN
      CAST('create database' AS pg_catalog.text)
    ELSE
      CAST('' AS pg_catalog.text)
  END role_attributes
FROM pg_catalog.pg_user
ORDER BY role_name desc;
```
 If you forgot password, the find PG_HBA.CONF in 
 C:\SAS\Config\Lev1\WebInfrastructurePlatformDataServer\data

 Change MD5 to TRUST and restart the service
 Now you can login without password

 ALTER USER <username> WITH PASSWORD '<new_password>';

 Change TRUST back to MD5 and restart.
