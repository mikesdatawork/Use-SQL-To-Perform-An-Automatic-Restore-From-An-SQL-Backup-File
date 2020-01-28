![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL To Perform An Automatic Restore From An SQL Backup File
**Post Date: April 8, 2014**        

## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>You have a bunch of SQL Database Restores you have to do, and a ton of backup files.  If you're like me you would prefer to code everything out.  Just write the logic, and make things alot easier.  I wrote some restore logic that might help you.   All you need to do is provide the database name, and the location to backup file.
Ok so what does this do exactly?
It takes the backup file you have, and extrapolates the Logical, and Physical name from the file, and passes it into a restore.   The restore has the following ( with replace, recovery ) so it will definitely overwrite the existing database that it's restoring too.
Dependencies:
Sp_KillAllProcessInDB
Can be found here:  http://techresource.appvui.biz/1547686/ms-sql-server-kill-processes-particular-db.html</p> 


## SQL-Logic
```SQL
use master;
set nocount on
declare @database_name varchar(255)
declare @backup_file_name varchar(255)
set @database_name = 'MyDatabaseNameHere'
set @backup_file_name = 'MyPathToBackupFileHere'
declare @filelistonly table
(
logicalname nvarchar(128)
, physicalname nvarchar(260)
, [type] char(1)
, filegroupname nvarchar(128)
, size numeric(20,0)
, maxsize numeric(20,0)
, fileid bigint
, createlsn numeric(25,0)
, droplsn numeric(25,0)
, uniqueid uniqueidentifier
, readonlylsn numeric(25,0)
, readwritelsn numeric(25,0)
, backupsizeinbytes bigint
, sourceblocksize int
, filegroupid int
, loggroupguid uniqueidentifier
, differentialbaselsn numeric(25,0)
, differentialbaseguid uniqueidentifier
, isreadonl bit
, ispresent bit
, tdethumbprint varbinary(32)
)
insert into
@filelistonly exec ('restore filelistonly from disk = "' + @backup_file_name + "")
declare @restore_line0 varchar (255)
declare @restore_line1 varchar (255)
declare @restore_line2 varchar (255)

declare @stats varchar (255)
declare @move_files varchar (max)
set @restore_line0 = ('use master;')
set @restore_line1 = ('exec master..sp_killallprocessindb "' + @database_name + "';')
set @restore_line2 = (select 'restore database [' + @database_name + '] from disk = "' + @backup_file_name + "' with replace, recovery, ')
set @stats = ('stats = 20;')
set @move_files = "
select @move_files = @move_files + 'move "' + logicalname + "' to "' + physicalname + "',' + char(10) from @filelistonly order by fileid asc
exec
(
@restore_line0
+ @restore_line1
+ @restore_line2
+ @move_files
+ @stats
)
```


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)


   
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")
 
