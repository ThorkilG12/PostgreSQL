## Read from Aiven PostgreSQL throgh ODBC and PowerShell ##

Aiven.io offers a 5GB PostgreSQL instance for free.
I have followed the setup samples from the Quick Connect page on the website, and I can connect from PSQL, pgAdmin and PHP.

The Quick Connect page does not have a sample about ODBC. Here you have some inspiration.
![image](https://github.com/ThorkilG12/PostgreSQL/assets/12120277/8abde962-a6e2-49da-8c58-0f50e64bdcf7)

From the Aiven Website I have got a file called ca.pem. Take a copy and pleace it in
C:\Users\Administrator\AppData\Roaming\postgresql
rename ca.pem to root.crt
Choose a database that exist, and press "Test"
```
In PowerShell do:
$query='select * from cities'
$conn = New-Object System.Data.Odbc.OdbcConnection
$conn.ConnectionString = "DSN=Aiven;DATABASE=de_tables"
$conn.open()
$cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)
$ds = New-Object system.Data.DataSet
$numrows = (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) 
$conn.close()
foreach ($row in $ds.Tables[0].Rows ) {
    write-host "$($row[1]) `t $($row.length)"
}
Write-host "Number of rows returned: $numrows"
$ds.tables[0].Rows | Out-GridView
```
As you can see, the name of the database in the ODBC setting can be overwrittes in PowerShell
