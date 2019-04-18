# Configuration&Setting

### Configuration & Setting Overview

Firstly, copied MariaDB database from Raspberry  Pi to windows server MariaDB. Secondly, we copied views, functions and procedure to copied database. Third, updated our views in LCCI_DB to refer copied database. 

 After tables, views, functions and procedures completed, we test run procedure. As it finished, Check lcci_zoneocc and lcci_zonedailyprofile has been generated successfully.  

Fixing alpha components is the next we took, mostly it was connection string changes; for example change database name from om_main to om_2101. Other changed is some fields name; we found some fieldname change for example  Node_ID1 to Node_ID , LastUpdate1 to LastUpdate.  

After everything seems good, we went to setup replication in windows server MariaDB. Firstly we have to give unique server_id then put MariaDB Master detail to configuration file in this case my.ini file. You might need to restart MariaDB. Check if Slave process started ; Lastly, if the data has been replicated successfully. The last step when replication running we change live data connection string to Windows Server. 

 
### Improvement 

* current occupancy status 
* current zone temperature, and  
* systems status getting from data from windows server, it reduce Rasbery Pi call for big amount for live data. 
* Live data seems pretty fast as it get data from windows server 
* When rasberry pi offline due to WIFI dongle issue system status and other live data still looks fine, before when rasberry pi goes offline users will get loads longer before it throw red errors like : can't connect 10.8.0.42; now it still display data even if it is not latest data 
* Procedure still required scheduled start at 3:00 AM. It takes around 2 hours, check lcci_zoneocc and lcci_zonedailyprofile. Do not stop the process call lcci_doAll when you test it manually or there will be duplicate procedure running. You can check by run: show processlist; normally 1 line saying query updating lcci_zonedailyprofile 

Demo 
[Smartdesk LCCI UK](http://smartdesk.ddns.net/tim/admin.a5w )
---




### Copying the Database
1. Copy MDBbu.sql.tar.gz 
\\10.8.0.42\mariadbbackup 
2. Create database Om_2101 
3. Restore database from dump file (it takes longer please wait)
```
mysql.exe -uroot -pseaford -hlocalhost OM_2101 < C:\easy-pc\files\home\occmon\Mariadb\data_backup\MDBbu.sql
```
4. Copy view to create chart to  OM_2101 
5. Edit view in LCCI_DB; change referenced database to OM_OM_2101 
6. Copy procedure and function to do create tables lcci_zoneocc and lcci_zonedailyprofile 
7. Check procedure by running  call LCCI_DoAll 
8. Check if procedure success 
9. Make sure no query update running, that  one indicator procedure has been finished, 
```MYSQL
show processlist; 
```
10. Check these table  lcci_zoneocc and lcci_zonedailyprofile has current date 
---




### Setting up Replication  

1. Go to server MariaDB 

2. Edit my.ini 

3. Add unique server_id 

4. Save Configuration 

5. Restart MariaDB on Windows Server  

6. Add detail MariaDB of Raspberry Pi as Master 

change master to

```MYSQL
MASTER_HOST=’10.8.0.42’, 

MASTER_USER='omrepuser', 

MASTER_PASSWORD='OCCupancy', 

MASTER_PORT=3306, 

MASTER_LOG_FILE= ‘mysqld-bin.000006’, 

MASTER_LOG_POS=328, 

MASTER_CONNECT_RETRY=10; 
```
7. Check if replication has been running 
Run command  to start replication 
```MYSQL
START SLAVE; 
```
8. Run to check if replication running 
```MYSQL
SHOW SLAVE STATUS \G ; 
``` 
> :memo:
Make sure that:<br>
Slave_IO_Running: Yes <br>
Slave_SQL_Running: Yes <br>
---
>:bulb: Check in the table OM_events: <br>
1. Compare OM_events in Raspberry Pi and Windows Server, <br>
2. check the DateTime field , if success you will see exact same records between Rasberry Pi and Windows Server MariaDB. <br>
3. Don't forget to refresh your navicat for both table at the same time so the record will be exactly the same. <br>

 ![Screenshot](../img/cs1.png)
---




### Fixing the Components  

1. Edit connection string in Alpha Five 

2. Change field name refer to new database; eg. Node_ID1 to Node_ID , LastUpdate1 to LastUpdate 

3. Publish server to development server 

4. Deploy to Production Server http://smartdesk.ddns.net/tim/admin.a5w 

5. Check components display as expected 

 

For Mobile 
We do the same as desktop components mostly changing connection string to copied database (om_2101) 
---
