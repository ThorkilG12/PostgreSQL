On a windows server you can "get in" even if you dont know anything, but the port-number that PostgreSQL is using.

By looking at the service that starts Postgres you can find the data directory, and here you have a file called PG_HBA.CONF

Edit the line
host    all             all             all                     md5
change "md5" to "trust" and restart the PostgreSQL service

Now find the postgres \bin folder and chacge directory in a CMD prompt

createuser -p 5432 -U postgres --createdb --login --superuser joedoe

createdb -p 9432 -U joedoe joedoe

psql -p 9432 -U joedoe
Now you cas se users by using \du
Databases: SELECT datname FROM pg_catalog.pg_database;


If you are logging into the postgres installation on a SAS Server, it might be that the user postgres does not exists. Try using -U dbmsowner
The database postgres exists, so 



