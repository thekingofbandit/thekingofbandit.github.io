# Trigger
We have some procedure to run to create:

* lcci_zonedailyprofile
* lcci_zoneocc
* lcci_temperature




### 1. P_LCCI_01
```MYSQL
CREATE DEFINER=`AdminDB`@`%` PROCEDURE `P_LCCI_01`(IN `IgnoreWeekends` tinyint(1))
    DETERMINISTIC
BEGIN
   /*
   *******************************************************************
   * Create table of parameters used primarily by the LCCI Dashboard *
   *******************************************************************
   */

   DROP TABLE IF EXISTS `OM_2101`.`LCCI_Params`;

   CREATE TABLE `OM_2101`.`LCCI_Params`
   (
      Param    VARCHAR(30),
      Value    VARCHAR(30)
   );

   INSERT INTO `OM_2101`.`LCCI_Params`
        VALUES ("Profile Zone Name", "Meeting Room 7"),
               ("Profile Start Date", "01/06/18"),
               ("Profile Stop Date", "30/06/18"),
               ("Profile Date", "25/06/18");

   /*
   **************************************************************
   * Create table tracking only Occupancy Start and Stop times. *
   **************************************************************
   */

   DROP TABLE IF EXISTS `OM_2101`.`LCCI_ZoneOcc`;

   CREATE TABLE `OM_2101`.`LCCI_ZoneOcc`
   (
      Zone        VARCHAR(50),
      start       DATETIME(3),
      stop        DATETIME(3),
      duration    TIME(3)
   );

   INSERT INTO `OM_2101`.`LCCI_ZoneOcc`
      SELECT Zone,
             start,
             stop,
             TIMEDIFF(stop, start) AS duration
        FROM (  SELECT Status,
                       (@vzone <> Zone)
                          AS new_zone,
                       @vstart :=
                          IF(Status = "Occ" AND @vstart IS NULL,
                             `DateTime`,
                             @vstart),
                       @vfirststart :=
                          IF(Status = "Unocc", @vstart, `DateTime`)
                          AS start,
                       @vstart := IF(Status = "Unocc", NULL, @vstart),
                       @vstop := IF(Status = "Unocc", `DateTime`, NULL)
                          AS stop,
                       @vzone := Zone
                          AS Zone
                  FROM `OM_2101`.`OMzoneocc`
              --    WHERE Zone = "Rectangular Table 1"  -- For debugging only
              ORDER BY Zone, `DateTime`) AS tmp,
             (SELECT @vstart := NULL,
                     @vfirststart := NULL,
                     @vstop := NULL,
                     @vzone := NULL) AS vars
       WHERE new_zone = 0 AND start IS NOT NULL AND stop IS NOT NULL;

   -- *********************************
   -- * Create a view of unique dates *
   -- *********************************

   CREATE OR REPLACE DEFINER = `AdminDB` @`%` SQL SECURITY DEFINER VIEW OM_2101.`v_lcci_dates`
   (
      date
   )
   AS
      SELECT DISTINCT DATE(`OM_2101`.`LCCI_ZoneOcc`.`start`) AS `date`
        FROM `OM_2101`.`LCCI_ZoneOcc`;

   -- *********************************
   -- * Create a view of unique zones *
   -- *********************************

   CREATE OR REPLACE DEFINER = `AdminDB` @`%` SQL SECURITY DEFINER VIEW OM_2101.`v_lcci_zones`
   (
      zone
   )
   AS
      SELECT DISTINCT `OM_2101`.`LCCI_ZoneOcc`.`zone` AS `zone`
        FROM `OM_2101`.`LCCI_ZoneOcc`;

   -- **********************************************************************************************
   -- * Cross join the zones and dates and hours to create a zone / date / hour / zoneuse template *
   -- **********************************************************************************************

   DROP TABLE IF EXISTS `LCCI_ZoneDailyProfile`;

   CREATE TABLE `LCCI_ZoneDailyProfile`
   (
      Zone       VARCHAR(50),
      Date       DATE,
      Hour       TINYINT,
      ZoneUse    FLOAT
   );

   INSERT INTO `LCCI_ZoneDailyProfile`
      SELECT OM_2101.`v_lcci_zones`.zone AS Zone,
             OM_2101.`v_lcci_dates`.date AS Date,
             myseq.seq AS Hour,
             0 AS ZoneUse
        FROM seq_0_to_23 AS myseq
             JOIN OM_2101.`v_lcci_dates`
             JOIN OM_2101.`v_lcci_zones`
       WHERE NOT (    IgnoreWeekends = TRUE
                  AND (DAYOFWEEK(Date) = 1 OR DAYOFWEEK(Date) = 7));

   -- ****************************************
   -- * Populate lcci_zonedailyprofile table *
   -- ****************************************

   UPDATE `LCCI_ZoneDailyProfile` AS t1
      SET zoneuse =
             (SELECT IFNULL(
                        SUM(
                           DAILYPROFILE(
                              TIMESTAMPADD(HOUR, t1.Hour, t1.Date),
                              TIMESTAMPADD(HOUR,
                                           HOUR(t2.start),
                                           DATE(t2.start)),
                              TIMESTAMPADD(HOUR,
                                           HOUR(t2.stop),
                                           DATE(t2.stop)),
                              t2.start,
                              t2.stop,
                              t2.duration)),
                        0)
                FROM `LCCI_ZoneOcc` AS t2
               WHERE     t1.Zone = t2.Zone
                     AND TIMESTAMPADD(HOUR, t1.Hour, t1.Date) >=
                         TIMESTAMPADD(HOUR, HOUR(t2.start), DATE(t2.start))
                     AND TIMESTAMPADD(HOUR, t1.Hour, t1.Date) <=
                         TIMESTAMPADD(HOUR, HOUR(t2.stop), DATE(t2.stop)));
END
```



### 2. P_LCCI_02
```MYSQL
CREATE DEFINER=`AdminDB`@`%` PROCEDURE `P_LCCI_02`(
   IN `Pzone`        varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci,
   IN `Pstartdate`   varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci,
   IN `Pstopdate`    varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci)
    DETERMINISTIC
BEGIN
   /*
   Average useage profile calculations for LCCI and a selected zone between a given
   start and stop date.
   */

   -- *********************************************************
   -- * Set Parameters for averaging in the LCCI_Params table *
   -- *********************************************************

   UPDATE LCCI_Params
      SET value = Pzone
    WHERE Param = "Profile Zone Name";

   UPDATE LCCI_Params
      SET value = Pstartdate
    WHERE Param = "Profile Start Date";

   UPDATE LCCI_Params
      SET value = Pstopdate
    WHERE Param = "Profile Stop Date";

   -- ********************************************************************************************
   -- * Create a view for for the LCCI average profile between the selected start and stop dates *
   -- ********************************************************************************************

   CREATE OR REPLACE DEFINER = `AdminDB` @`%` SQL SECURITY DEFINER VIEW OM_2101.v_lcci_profileavg
   (
      `Zone`,
      `Hour`,
      `ZoneUse`
   )
   AS
        SELECT "LCCI Average" AS `Zone`,
               t.`Hour` AS `Hour`,
               AVG(t.`ZoneUse`) AS `ZoneUse`
          FROM `OM_2101`.`LCCI_ZoneDailyProfile` AS t
         WHERE     t.date >=
                   str_to_date((SELECT tp.value
                                  FROM LCCI_Params AS tp
                                 WHERE tp.param = "Profile Start Date"),
                               "%d/%m/%y")
               AND t.date <=
                   str_to_date((SELECT tp.value
                                  FROM LCCI_Params AS tp
                                 WHERE tp.param = "Profile Stop Date"),
                               "%d/%m/%y")
      GROUP BY Hour;

   -- ********************************************************************************************
   -- * Create a view for for the zone average profile between the selected start and stop dates *
   -- ********************************************************************************************

   CREATE OR REPLACE DEFINER = `AdminDB` @`%` SQL SECURITY DEFINER VIEW OM_2101.v_zone_profileavg
   (
      `Zone`,
      `Hour`,
      `ZoneUse`
   )
   AS
        SELECT t.zone AS `Zone`,
               t.`Hour` AS `Hour`,
               AVG(t.`ZoneUse`) AS `ZoneUse`
          FROM `OM_2101`.`LCCI_ZoneDailyProfile` AS t
         WHERE     t.`Zone` = (SELECT tp.value
                                 FROM LCCI_Params AS tp
                                WHERE tp.param = "Profile Zone Name")
               AND t.date >=
                   str_to_date((SELECT tp.value
                                  FROM LCCI_Params AS tp
                                 WHERE tp.param = "Profile Start Date"),
                               "%d/%m/%y")
               AND t.date <=
                   str_to_date((SELECT tp.value
                                  FROM LCCI_Params AS tp
                                 WHERE tp.param = "Profile Stop Date"),
                               "%d/%m/%y")
      GROUP BY Hour;
END
```






### 3. P_LCCI_03
```MYSQL
CREATE DEFINER=`AdminDB`@`%` PROCEDURE `P_LCCI_03`(
   IN `Pdate`        varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci,
   IN `Pstartdate`   varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci,
   IN `Pstopdate`    varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci)
    DETERMINISTIC
BEGIN
   /*
   Average useage profile calculations for LCCI and a selected zone between a given
   start and stop date.
   */

   -- *********************************************************
   -- * Set Parameters for averaging in the LCCI_Params table *
   -- *********************************************************

   UPDATE LCCI_Params
      SET value = Pdate
    WHERE Param = "Profile Date";

   UPDATE LCCI_Params
      SET value = Pstartdate
    WHERE Param = "Profile Start Date";

   UPDATE LCCI_Params
      SET value = Pstopdate
    WHERE Param = "Profile Stop Date";

   -- ********************************************************************************************
   -- * Create a view for for the LCCI average profile between the selected start and stop dates *
   -- ********************************************************************************************

   CREATE OR REPLACE DEFINER = `AdminDB` @`%` SQL SECURITY DEFINER VIEW OM_2101.v_lcci_profileavg
   (
      `Zone`,
      `Hour`,
      `ZoneUse`
   )
   AS
        SELECT "LCCI Average" AS `Zone`,
               t.`Hour` AS `Hour`,
               AVG(t.`ZoneUse`) AS `ZoneUse`
          FROM `OM_2101`.`LCCI_ZoneDailyProfile` AS t
         WHERE     t.date >=
                   str_to_date((SELECT tp.value
                                  FROM LCCI_Params AS tp
                                 WHERE tp.param = "Profile Start Date"),
                               "%d/%m/%y")
               AND t.date <=
                   str_to_date((SELECT tp.value
                                  FROM LCCI_Params AS tp
                                 WHERE tp.param = "Profile Stop Date"),
                               "%d/%m/%y")
      GROUP BY Hour;

   -- *******************************************************************
   -- * Create a view for for the average profile on the specified date *
   -- *******************************************************************

   CREATE OR REPLACE DEFINER = `AdminDB` @`%` SQL SECURITY DEFINER VIEW OM_2101.v_date_profileavg
   (
      `Date`,
      `Hour`,
      `ZoneUse`
   )
   AS
        SELECT t.Date AS `Date`,
               t.`Hour` AS `Hour`,
               AVG(t.`ZoneUse`) AS `ZoneUse`
          FROM `OM_2101`.`LCCI_ZoneDailyProfile` AS t
         WHERE t.date = str_to_date((SELECT tp.value
                                       FROM LCCI_Params AS tp
                                      WHERE tp.param = "Profile Date"),
                                    "%d/%m/%y")
      GROUP BY Hour;
END
```






### 4. P_LCCI_04
```MYSQL
CREATE DEFINER=`AdminDB`@`%` PROCEDURE `P_LCCI_04`(IN `TWF` float, IN `TempMax` float)
    DETERMINISTIC
    COMMENT 'Procedure to create a Temperature Average.'
BEGIN
   /*
   Procedure for working with Temperatures.  This could be adapted to handle
   any other spacially and time varying data.
   In this initial form an average temperature is calculated using the formula:
          Tav(t) = (Tav(t-1) * (1-TWF)) + (T(t) * TWF)
         where: Tav(t) is the average temperature at time 't'
        Tav(t-1) is the average temperature at the time prior to 't'.
        TWF is a weighting factor
        T(t) is the temperature at time 't'
   Parameters:
       TWF:     Temperature Weighting Factor. See above
       TempMax: Temperatures above this value will be ignored i.e. they are assumed to be invalid.
   */

   DROP TABLE IF EXISTS `OM_2101`.`LCCI_Temperature`;

   CREATE TABLE `OM_2101`.`LCCI_Temperature`
   (
      rid                INT,
      datetime           DATETIME(3),
      Location           VARCHAR(50),
      Zone               VARCHAR(50),
      Loc_Temp           FLOAT,
      Office_Temp_Avg    FLOAT
   );

   INSERT INTO `OM_2101`.`LCCI_Temperature`
      SELECT rid,
             datetime,
             Location,
             Zone,
             Loc_Temp,
             Office_Temp_Avg
        FROM (  SELECT i.`DateTime`
                          AS datetime,
                       i.`Location`
                          AS Location,
                       i.`SensorType`,
                       i.`Zone`
                          AS Zone,
                       i.`Value`
                          AS Loc_Temp,
                       @TempAvg :=
                          IF(@TempAvg IS NULL,
                             i.`Value`,
                             (@TempAvg * (1 - TWF) + i.`Value` * TWF))
                          AS Office_Temp_Avg,
                       @counter := IF(@counter IS NULL, 1, @counter + 1)
                          AS rid
                  FROM `OM_2101`.`OMevents` AS i
                 WHERE     i.`SensorType` LIKE "%Temperature%"
                       AND i.`Value` < TempMax
              ORDER BY i.`DateTime` ASC) AS tmp,
             (SELECT @TempAvg := NULL, @counter := 0) AS vars;
END
```



### 5. LCCI_Do
```MYSQL
CREATE DEFINER=`AdminDB`@`%` PROCEDURE `LCCI_Do`()
BEGIN
 -- *************************
-- * Do All LCCI Worksheet *
-- *************************

/*
Procedure for creating:
	lcci_zoneocc
	lcci_zonedailyprofile
	lcci_params
Parameters: 
	IgnoreWeekends 0 = False, 1 = True
Normally week-ends should be ignored.
Further enhancement is required to handle public holidays.
*/
Call P_LCCI_01(1);

/*
Procedure for calculating profile averages:
	v_lcci_profileavg
	v_zone_profileavg
Parameters:
	Profile Zone Name: 	The zone name for which the profile will be calculated
	Start Date: The date from which the calculations are made
	Stop Date: The date on which the calculations stop
*/
Call P_LCCI_02("Meeting Room 7", "31/05/18", "02/07/18");

/*
Procedure for calculating profile averages:
	v_lcci_profileavg
	v_date_profileavg
Parameters:
	Profile Date: 	The date for which the profile will be calculated
	Start Date: The date from which the calculations are made
	Stop Date: The date on which the calculations stop
*/
Call P_LCCI_03("27/06/18", "31/05/18", "02/07/18");

/*
   Procedure for working with Temperatures.  This could be adapted to handle
   any other spacially and time varying data.
   The procedure creates a table om_main.LCCI_Temperature which extracts relevant
   information from om_ev.omevents.
   In this initial form an average office temperature is calculated using the formula:
          Tav(t) = (Tav(t-1) * (1-TWF)) + (T(t) * TWF)
         where: Tav(t) is the average temperature at time 't'
        Tav(t-1) is the average temperature at the time prior to 't'.
        TWF is a weighting factor
        T(t) is the temperature at time 't'
   Parameters:
       TWF:     Temperature Weighting Factor. See above
       TempMax: Temperatures above this value will be ignored i.e. they are assumed to be invalid.
 
*/
Call P_LCCI_04(0.02,100);
END
```





##Problem on version 2 LCCI
trigger not run, you can check these two tables; 
the table is not being updated, or the table empty, 

* lcci_zonedailyprofile
* lcci_zoneocc

for the rid field in lcci_temperature, this always showing increment 1


###fix trigger not run

1. re run and check 3 or more times
2. make sure in processlist mysql should showing something like this, 
so it means lcci_zonedailyprofile has been created
3. Do not wait; it takes 2 hours; you must may consider run in UK server instead of remote navicat
```
15635 | AdminDB         | %:50080              | om_2101 | Query     |    125 | Sending data                                                                | UPDATE `LCCI_ZoneDailyProfile` AS t1
      SET zoneuse =
             (SELECT IFNULL(
            |    0.000 |
```



