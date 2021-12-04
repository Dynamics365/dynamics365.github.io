# Rename a Database in SQL Server


1. Open `Microsoft SQL Server Management Studio`.

2. Connect to the server where in the DB you want to rename is located. 


3. Modify the following script and run it

```sql
-- Replace all MyDBs with the name of the DB you want to change its name
USE [MyDB];
-- Changing Physical names and paths
-- Replace all NewMyDB with the new name you want to set for the DB
-- Replace 'C:...NewMyDB.mdf' with full path of new DB file to be used
ALTER DATABASE MyDB MODIFY FILE (NAME = ' MyDB ', FILENAME = 'C:...NewMyDB.mdf');
-- Replace 'C:...NewMyDB_log.ldf' with full path of new DB log file to be used
ALTER DATABASE MyDB MODIFY FILE (NAME = ' MyDB _log', FILENAME = 'C:...NewMyDB_log.ldf');
-- Changing logical names
ALTER DATABASE MyDB MODIFY FILE (NAME = MyDB, NEWNAME = NewMyDB);
ALTER DATABASE MyDB MODIFY FILE (NAME = MyDB _log, NEWNAME = NewMyDB_log);
```

4. Right click on the DB and select `Tasks>Take Offline`


5. Go to the location that `MDF` and `LDF files` are located and rename them exactly as you specified in first two alter commands. If you changed the folder path, then you need to move them there.


6. Go back to `Microsoft SQL Server Management Studio` and right click on the DB and select `Tasks>Bring Online`.


