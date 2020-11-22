sas的code日志跟踪分析



# SASTRACE= SAS System Option

Generates trace information from a DBMS engine.

| Valid in:    | configuration file, SAS invocation, OPTIONS statement        |
| ------------ | ------------------------------------------------------------ |
| Category:    | Log and procedure output control: SAS log                    |
| Default:     | none                                                         |
| Data source: | Amazon Redshift, Aster, DB2 under UNIX and PC Hosts, DB2 under z/OS, Google BigQuery, Hadoop, HAWQ, Impala, Informix, JDBC, Microsoft SQL Server, MySQL, Netezza, ODBC, OLE DB, Oracle, PostgreSQL, SAP ASE, SAP HANA, SAP IQ, Snowflake, Spark, Teradata, Vertica |
| Note:        | Support for Google BigQuery and Snowflake was added in the August 2019 release of SAS/ACCESS |
| Tip:         | You can also use more than one SASTRACE= option at a time if the arguments are for different positions in the argument list (for example, `SASTRACE=',,d,d'`). The value 'd,' cannot be combined with other values. |
| See:         | [SASTRACELOC= system option](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=p10zkrs6rnpiv2n1b7jkrbdgq4bd.htm&locale=en), [Generating Trace Information for Threaded Reads](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=n1uyd3vmzdti5en1r7061u5bsc5m.htm&locale=en) |

Table of Contents

[Syntax](https://go.documentation.sas.com/#p06pkfykej0vyxn1wbjkfbsokxtt)

[Syntax Description](https://go.documentation.sas.com/#p1r9u87a9d9qomn1792wqyuwokv6)

[Details](https://go.documentation.sas.com/#n106wtrb9ruaiwn1f57vukujdgvz)

[Examples](https://go.documentation.sas.com/#p05t5uirgejowpn1ciklp4t4g44p)

[Example 1: Use SQL Trace ',,,d'](https://go.documentation.sas.com/#n0vg6cyj7g23syn1o57cc5g1ewyq)

[Example 2: Use Log Trace ',,d'](https://go.documentation.sas.com/#n0fcq5i8aa8gxhn1qxnemoizmw9v)

[Example 3: Use DBMS Trace 'd,'](https://go.documentation.sas.com/#p04h920kyuu1lrn1x1unl3h4a0n6)

[Example 4: Use Brief SQL Trace ',,,db'](https://go.documentation.sas.com/#p0q8euqqzb6qjnn19mti2xx3597t)

[Example 5: Use Time Trace ',,,s'](https://go.documentation.sas.com/#n13kpx36mkdxwon1cvqxhkwdvh0j)

[Example 6: Use Time All Trace ',,,sa'](https://go.documentation.sas.com/#n1n8rjpeds2aapn1858zreouxyz7)

[Example 7: Use Threaded Trace ',,t,'](https://go.documentation.sas.com/#n1tw0dyqat5kqtn180ir104iej6l)

## Syntax

SASTRACE=[ ',,,d'](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=n0732u1mr57ycrn1urf24gzo38sc.htm&locale=en#n0pfunyfqr7vx9n1rqwdyaasaycv) | [' ,,d,'](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=n0732u1mr57ycrn1urf24gzo38sc.htm&locale=en#p1qu8ophl20cftn1kzc1oju0twwa) | [' d,'](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=n0732u1mr57ycrn1urf24gzo38sc.htm&locale=en#p1ibe95cw4sgyrn1nbtwc1b9qwem) | ['d,,,'](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=n0732u1mr57ycrn1urf24gzo38sc.htm&locale=en#n1odzgkeb4sh1nn185ct19c1je6r) | [',,,db'](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=n0732u1mr57ycrn1urf24gzo38sc.htm&locale=en#p1x8neslhpiqpln1j8wr69nsgk7f) | [' ,,,s'](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=n0732u1mr57ycrn1urf24gzo38sc.htm&locale=en#p1ne3a2tp08bsun17au4ppk1bt0f) | [' ,,,sa'](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=n0732u1mr57ycrn1urf24gzo38sc.htm&locale=en#p0e4yh92i84oydn1ld9n4n7qxlxl) | [',,t,'](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=n0732u1mr57ycrn1urf24gzo38sc.htm&locale=en#p1pfogb21wgdd0n0ztqc61028l0p)

### Syntax Description

#### ',,,d'



specifies that all [SQL](https://go.documentation.sas.com/) statements that are sent to the [DBMS](https://go.documentation.sas.com/) are sent to the log. Here are the applicable statements:

| SELECT | DELETE         |
| ------ | -------------- |
| CREATE | SYSTEM CATALOG |
| DROP   | COMMIT         |
| INSERT | ROLLBACK       |
| UPDATE |                |

For [engines](https://go.documentation.sas.com/) that do not generate SQL statements, API calls and all parameters are sent to the log.

#### ',,d,'

specifies that all routine calls are sent to the log. All function enters, exits, and pertinent parameters and return codes are traced when you select this option. The information varies from engine to engine, however.

This option is most useful if you have a problem and need to send a SAS log to technical support for troubleshooting.

#### 'd,'

specifies that all DBMS calls (such as API and [client](https://go.documentation.sas.com/) calls, connection information, column bindings, column error information, and row processing) are sent to the log. This information varies from engine to engine, however.

This option is most useful if you have a problem and need to send a SAS log to technical support for troubleshooting.

#### 'd,,,'

specifies that version information for the current DBMS and client are displayed in the log.

#### ',,,db'

specifies that only a brief version of all SQL statements that the ',,,d' option normally generates are sent to the log.

#### ',,,s'

specifies that a summary of timing information for calls made to the DBMS is sent to the log.

#### ',,,sa'

specifies that timing information for each call that is made to the DBMS is sent to the log along with a summary.

#### ',,t,'

specifies that all threading information is sent to the log. Here is the information that it includes:

- number of threads that are spawned
- number of observations that each thread contains
- exit code of the thread, if it fails

## Details

SASTRACE= and [SASTRACELOC=](https://go.documentation.sas.com/?cdcId=pgmsascdc&cdcVersion=9.4_3.4&docsetId=acreldb&docsetTarget=p10zkrs6rnpiv2n1b7jkrbdgq4bd.htm&locale=en) behavior is specific to SAS/ACCESS software. SASTRACE= is a very powerful tool to use when you want to see the commands that SAS/ACCESS sent to your DBMS. SASTRACE= output is DBMS-specific. However, most SAS/ACCESS engines show you statements like SELECT or COMMIT as the DBMS processes them for the SAS application. These details can help you manage SASTRACE= output in your DBMS.



- When using SASTRACE= on PC platforms, you must also specify SASTRACELOC=.

- Here is how to turn SAS tracing off:

  ```
  options sastrace=off;
  ```

- Log output is much easier to read if you specify NOSTSUFFIX. Because this code is entered without specifying the option, the resulting log is longer and harder to decipher.

  Note: NOSTSUFFIX is not supported on z/OS.

  ```
  options sastrace=',,,d' sastraceloc=saslog;
  proc print data=mydblib.snow_birthdays;
  run;
  ```

  Here is the resulting log.

  ```
  0 1349792597 sastb_next 2930 PRINT
  ORACLE_5: Prepared: 1 1349792597 sastb_next 2930 PRINT
  SELECT * FROM scott.SNOW_BIRTHDAYS 2 1349792597 sastb_next 2930 PRINT
  3 1349792597 sastb_next 2930 PRINT
  16 proce print data=mydblib.snow_birthdays; run;
  4 1349792597 sastb_next 2930 PRINT
  ORACLE_6: Executed: 5 1349792597 sastb_next 2930 PRINT
  Prepared statement ORACLE_5 6 1349792597 sastb_next 2930 PRINT
  7 1349792597 sastb_next 2930 PRINT
  ```

  Use NOSTSUFFIX to make the log file much easier to read.

  ```
  options sastrace=',,,d' sastraceloc=saslog nostsuffix;
  proc print data=mydblib.snow_birthdays;
  run;
  ```

  Here is the resulting log.

  ```
  ORACLE_1: Prepared:
  SELECT * FROM scott.SNOW_BIRTHDAYS
  12 proc print data=mydblib.snow_birthdays; run;
  ORACLE_2: Executed:
  Prepared statement ORACLE_1
  ```



## Examples

### Example 1: Use SQL Trace ',,,d'

These examples use NOSTSUFFIX and SASTRACELOC=SASLOG.

```
data work.winter_birthdays;
   input empid birthdat date9. lastname $18.;
   format birthdat date9.;
datalines;
678999 28DEC1966 PAVEO             JULIANA        3451
456788 12JAN1977 SHIPTON           TIFFANY        3468
890123 20FEB1973 THORSTAD          EDVARD         3329
;
run;
```

Examples are based on this [data set](https://go.documentation.sas.com/).

```
options sastrace=',,,d' sastraceloc=saslog nostsuffix;
libname mydblib oracle user=myusr1 password=mypwd1 schema=bday_data;
data mydblib.snow_birthdays;
   set work.winter_birthdays;
run;
libname mydblib clear;
```

Output for this ',,,d' example is written to the SAS log, as specified in the SASTRACELOC=SASLOG option.

SAS Log Output from the SASTRACE= ',,,d' System Option

```
30   data work.winter_birthdays;
31      input empid birthdat date9. lastname $18.;
32      format birthdat date9.;
33   datalines;
NOTE: The data set WORK.WINTER_BIRTHDAYS has 3 observations and 3 variables.
NOTE: DATA statement used (Total process time):
     real time           0.03 seconds
     cpu time            0.04 seconds
37   ;
38   run;
39   options sastrace=',,,d' sastraceloc=saslog nostsuffix;
40   libname mydblib oracle user=myusr1 password=XXXXX schema=bday_data;
NOTE: Libref MYDBLIB was successfully assigned as follows:
     Engine:        ORACLE
     Physical Name:
41   proc datasets library=mydblib;
  delete snow_birthdays;run;
ORACLE_1: Prepared:
SELECT * FROM SNOW_BIRTHDAYS
ORACLE_2: Executed:
DROP TABLE  SNOW_BIRTHDAYS
NOTE: Deleting MYDBLIB.SNOW_BIRTHDAYS (memtype=DATA).
NOTE: PROCEDURE DELETE used (Total process time):
     real time           0.26 seconds
     cpu time            0.12 seconds
42   data mydblib.snow_birthdays;
43      set work.winter_birthdays;
44   run;
ORACLE_3: Prepared:
SELECT * FROM SNOW_BIRTHDAYS
NOTE: SAS variable labels, formats, and lengths are not written to DBMS tables.
ORACLE_4: Executed:
CREATE TABLE SNOW_BIRTHDAYS(empid NUMBER ,birthdat DATE,lastname VARCHAR2 (18))
ORACLE_5: Prepared:
INSERT  INTO SNOW_BIRTHDAYS (empid,birthdat,lastname) VALUES
(:empid,TO_DATE(:birthdat,'DDMONYYYY','NLS_DATE_LANGUAGE=American'),:lastname)
NOTE: There were 3 observations read from the data set WORK.WINTER_BIRTHDAYS.
ORACLE_6: Executed:
Prepared statement ORACLE_5
ORACLE:  *-*-*-*-*-*-* COMMIT *-*-*-*-*-*-*
NOTE: The data set MYDBLIB.SNOW_BIRTHDAYS has 3 observations and 3 variables.
ORACLE:  *-*-*-*-*-*-* COMMIT *-*-*-*-*-*-*
NOTE: DATA statement used (Total process time):
     real time           0.47 seconds
     cpu time            0.13 seconds
ORACLE_7: Prepared:
SELECT * FROM SNOW_BIRTHDAYS
45   proc print data=mydblib.snow_birthdays; run;
ORACLE_8: Executed:
Prepared statement ORACLE_7
NOTE: There were 3 observations read from the data set MYDBLIB.SNOW_BIRTHDAYS.
NOTE: PROCEDURE PRINT used (Total process time):
     real time           0.04 seconds
     cpu time            0.04 seconds
46
47   libname mydblib clear;
NOTE: Libref MYDBLIB has been deassigned.
```

### Example 2: Use Log Trace ',,d'

```
options sastrace=',,d,' sastraceloc=saslog nostsuffix;
libname mydblib oracle user=myusr1 password=mypwd1 schema=bday_data;
data mydblib.snow_birthdays;
   set work.winter_birthdays;
run;
libname mydblib clear;
```

Output is written to the SAS log, as specified in the SASTRACELOC=SASLOG option.

SAS Log Output from the SASTRACE= ',,d,' System Option

```
84   options sastrace=',,d,' sastraceloc=saslog nostsuffix;
ACCESS ENGINE: Entering DBICON
ACCESS ENGINE: Number of connections is 1
ORACLE: orcon()
ACCESS ENGINE: Successful physical conn id 1
ACCESS ENGINE: Exiting DBICON, Physical Connect id = 1, with rc=0X00000000
85   libname mydblib oracle user=myusr1 password=mypwd1 schema=bday_data;
ACCESS ENGINE:  CONNECTION= SHAREDREAD
NOTE: Libref MYDBLIB was successfully assigned as follows:
      Engine:        ORACLE
      Physical Name: lupin
86   data mydblib.snow_birthdays;
87      set work.winter_birthdays;
88   run;
ACCESS ENGINE:  Entering yoeopen
ACCESS ENGINE: Entering dbiopen
ORACLE: oropen()
ACCESS ENGINE: Successful dbiopen, open id 0, connect id 1
ACCESS ENGINE: Exit dbiopen with rc=0X00000000
ORACLE: orqall()
ORACLE: orprep()
ACCESS ENGINE: Entering dbiclose
ORACLE: orclose()
ACCESS ENGINE: DBICLOSE open_id 0, connect_id 1
ACCESS ENGINE: Exiting dbiclos with rc=0X00000000
ACCESS ENGINE:  Access Mode is XO_OUTPUT
ACCESS ENGINE:  Access Mode is XO_SEQ
ACCESS ENGINE:  Shr flag is XHSHRMEM
ACCESS ENGINE: Entering DBICON
ACCESS ENGINE:  CONNECTION= SHAREDREAD
ACCESS ENGINE: Number of connections is 2
ORACLE: orcon()
ACCESS ENGINE: Successful physical conn id 2
ACCESS ENGINE: Exiting DBICON, Physical Connect id = 2, with rc=0X00000000
ACCESS ENGINE: Entering dbiopen
ORACLE: oropen()
ACCESS ENGINE: Successful dbiopen, open id 0, connect id 2
ACCESS ENGINE: Exit dbiopen with rc=0X00000000
ACCESS ENGINE: Exit yoeopen with SUCCESS.
ACCESS ENGINE:  Begin yoeinfo
ACCESS ENGINE: Exit yoeinfo with SUCCESS.
ORACLE: orovar()
NOTE: SAS variable labels, formats, and lengths are not written to DBMS tables.
ORACLE: oroload()
ACCESS ENGINE: Entering dbrload with SQL Statement set to
          CREATE TABLE SNOW_BIRTHDAYS(empid NUMBER ,birthdat DATE,lastname VARCHAR2 (18))
ORACLE: orexec()
ORACLE: orexec() END
ORACLE: orins()
ORACLE: orubuf()
ORACLE: orubuf()
ORACLE: SAS date : 28DEC1966
ORACLE: orins()
ORACLE: SAS date : 12JAN1977
ORACLE: orins()
ORACLE: SAS date : 20FEB1973
NOTE: There were 3 observations read from the data set WORK.WINTER_BIRTHDAYS.
ORACLE: orforc()
ORACLE: orflush()
NOTE: The data set MYDBLIB.SNOW_BIRTHDAYS has 3 observations and 3 variables.
ACCESS ENGINE:  Enter yoeclos
ACCESS ENGINE: Entering dbiclose
ORACLE: orclose()
ORACLE: orforc()
ORACLE: orflush()
ACCESS ENGINE: DBICLOSE open_id 0, connect_id 2
ACCESS ENGINE: Exiting dbiclos with rc=0X00000000
ACCESS ENGINE: Entering DBIDCON
ORACLE:  ordcon
ACCESS ENGINE: Physical disconnect on id = 2
ACCESS ENGINE: Exiting DBIDCON with rc=0X00000000, rc2=0X00000000
ACCESS ENGINE: Exit yoeclos with rc=0x00000000
NOTE: DATA statement used (Total process time):
      real time           0.21 seconds
      cpu time            0.06 seconds
ACCESS ENGINE: Entering DBIDCON
ORACLE:  ordcon
ACCESS ENGINE: Physical disconnect on id = 1
ACCESS ENGINE: Exiting DBIDCON with rc=0X00000000, rc2=0X00000000
89   libname mydblib clear;
NOTE: Libref MYDBLIB has been deassigned.
```

### Example 3: Use DBMS Trace 'd,'

```
options sastrace='d,' sastraceloc=saslog nostsuffix;
libname mydblib oracle user=myusr1 password=mypwd1 schema=bday_data;
data mydblib.snow_birthdays;
   set work.winter_birthdays;
run;
libname mydblib clear;
```

Output is written to the SAS log, as specified in the SASTRACELOC=SASLOG option.

SAS Log Output from the SASTRACE= 'd,' System Option

```
ORACLE: PHYSICAL connect successful.
ORACLE: USER=myusr1
ORACLE: PATH=mypath
ORACLE: SCHEMA=bday_data
110  libname mydblib oracle user=myusr1 password=mypwd1 path=mypath schema=bday_data;
NOTE: Libref MYDBLIB was successfully assigned as follows:
      Engine:        ORACLE
      Physical Name: mypath
111  data mydblib.snow_birthdays;
112     set work.winter_birthdays;
113  run;
ORACLE: PHYSICAL connect successful.
ORACLE: USER=myusr1
ORACLE: PATH=mypath
ORACLE: SCHEMA=bday_data
NOTE: SAS variable labels, formats, and lengths are not written to DBMS tables.
ORACLE:  INSERTBUFF option value set to 10.
NOTE: There were 3 observations read from the data set WORK.WINTER_BIRTHDAYS.
ORACLE:  Rows processed: 3
ORACLE:  Rows failed   : 0
NOTE: The data set MYDBLIB.SNOW_BIRTHDAYS has 3 observations and 3 variables.
ORACLE: Successfully disconnected.
ORACLE: USER=myusr1
ORACLE: PATH=mypath
NOTE: DATA statement used (Total process time):
      real time           0.21 seconds
      cpu time            0.04 seconds
ORACLE: Successfully disconnected.
ORACLE: USER=myusr1
ORACLE: PATH=mypath
114  libname mydblib clear;
NOTE: Libref MYDBLIB has been deassigned.
```

### Example 4: Use Brief SQL Trace ',,,db'

```
options sastrace=',,,db' sastraceloc=saslog nostsuffix;
libname mydblib oracle user=myusr1 password=mypwd1 path=mysrv1;
data mydblib.employee1;
   set mydblib.employee;
run;
```

Output is written to the SAS log, as specified in the SASTRACELOC=SASLOG option.

SAS Log Output from the SASTRACE= ',,,db' System Option

```
ORACLE_23: Prepared: on connection 2
SELECT * FROM EMPLOYEE
 19?
ORACLE_24: Prepared: on connection 3
SELECT * FROM EMPLOYEE1
NOTE: SAS variable labels, formats, and lengths are not written to DBMS
      tables.
ORACLE_25: Executed: on connection 4
CREATE TABLE EMPLOYEE1(NAME VARCHAR2 (20),ID NUMBER (5),CITY VARCHAR2
(15),SALARY NUMBER ,DEPT NUMBER (5))
ORACLE_26: Executed: on connection 2
SELECT statement  ORACLE_23
ORACLE_27: Prepared: on connection 4
INSERT  INTO EMPLOYEE1 (NAME,ID,CITY,SALARY,DEPT) VALUES
(:NAME,:ID,:CITY,:SALARY,:DEPT)
**NOTE**: ORACLE_27 on connection 4
The Execute statements associated with
this Insert statement are suppressed due to SASTRACE brief
setting-SASTRACE=',,,bd'. Remove the 'b' to obtain full trace.
NOTE: There were 17 observations read from the data set MYDBLIB.EMPLOYEE.
```

### Example 5: Use Time Trace ',,,s'

```
options sastrace=',,,s' sastraceloc=saslog nostsuffix;
libname mydblib oracle user=myusr1 password=mypwd1 schema=bday_data;
data mydblib.snow_birthdays;
   set work.winter_birthdays;
run;
libname mydblib clear;
```

Output is written to the SAS log, as specified in the SASTRACELOC=SASLOG option.

SAS Log Output from the SASTRACE= ',,,s' System Option

```
118  options sastrace=',,,s' sastraceloc=saslog nostsuffix;
119  libname mydblib oracle user=myusr1 password=mypwd1 schema=bday_data;
NOTE: Libref MYDBLIB was successfully assigned as follows:
      Engine:        ORACLE
      Physical Name: lupin
120  data mydblib.snow_birthdays;
121     set work.winter_birthdays;
122  run;
NOTE: SAS variable labels, formats, and lengths are not written to DBMS tables.
NOTE: There were 3 observations read from the data set WORK.WINTER_BIRTHDAYS.
NOTE: The data set MYDBLIB.SNOW_BIRTHDAYS has 3 observations and 3 variables.
Summary Statistics for ORACLE are:
Total SQL execution seconds were:                   0.127079
Total SQL prepare seconds were:                     0.004404
Total SQL row insert seconds were:                  0.004735
Total seconds used by the ORACLE ACCESS engine were     0.141860
NOTE: DATA statement used (Total process time):
      real time           0.21 seconds
      cpu time            0.04 seconds
123  libname mydblib clear;
NOTE: Libref MYDBLIB has been deassigned.
```

### Example 6: Use Time All Trace ',,,sa'

```
options sastrace=',,,sa' sastraceloc=saslog nostsuffix;
libname mydblib oracle user=myusr1 password=mypwd1 schema=bday_data;
data mydblib.snow_birthdays;
   set work.winter_birthdays;
run;
libname mydblib clear;
```

Output is written to the SAS log, as specified in the SASTRACELOC=SASLOG option.

SAS Log Output from the SASTRACE= ',,,sa' System Option

```
146  options sastrace=',,,sa' sastraceloc=saslog nostsuffix;
147
148  libname mydblib oracle user=myusr1 password=mypwd1 path=lupin schema=bday_data insertbuff=1;
NOTE: Libref MYDBLIB was successfully assigned as follows:
      Engine:        ORACLE
      Physical Name: lupin
149  data mydblib.snow_birthdays;
150     set work.winter_birthdays;
151  run;
NOTE: SAS variable labels, formats, and lengths are not written to DBMS tables.
ORACLE:  The insert time in seconds is    0.004120
ORACLE:  The insert time in seconds is    0.001056
ORACLE:  The insert time in seconds is    0.000988
NOTE: There were 3 observations read from the data set WORK.WINTER_BIRTHDAYS.
NOTE: The data set MYDBLIB.SNOW_BIRTHDAYS has 3 observations and 3 variables.
Summary Statistics for ORACLE are:
Total SQL execution seconds were:                   0.130448
Total SQL prepare seconds were:                     0.004525
Total SQL row insert seconds were:                  0.006158
Total seconds used by the ORACLE ACCESS engine were     0.147355
NOTE: DATA statement used (Total process time):
      real time           0.20 seconds
      cpu time            0.00 seconds
152
153  libname mydblib clear;
NOTE: Libref MYDBLIB has been deassigned.
```

### Example 7: Use Threaded Trace ',,t,'

```
options sastrace=',,t,' sastraceloc=saslog nostsuffix;
libname mydblib oracle user=myusr1 password=mypwd1 schema=bday_data;
data mydblib.snow_birthdays(DBTYPE=(empid'number(10');
   set work.winter_birthdays;
run;
proc print data=mydblib.snow_birthdays(dbsliceparm=(all,3));
run;
```

Output is written to the SAS log, as specified in the SASTRACELOC=SASLOG option.

SAS Log Output from the SASTRACE= ',,t,' System Option

```sas
165  options sastrace=',,t,' sastraceloc=saslog nostsuffix;
166  data mydblib.snow_birthdays(DBTYPE=(empid='number(10)'));
167     set work.winter_birthdays;
168  run;
NOTE: SAS variable labels, formats, and lengths are not written to DBMS tables.
NOTE: There were 3 observations read from the data set WORK.WINTER_BIRTHDAYS.
NOTE: The data set MYDBLIB.SNOW_BIRTHDAYS has 3 observations and 3 variables.
NOTE: DATA statement used (Total process time):
       real time          0.21 seconds
       cpu time           0.06 seconds
169  proc print data=mydblib.snow_birthdays(dbsliceparm=(all,3));
170  run;
ORACLE:  DBSLICEPARM option set and 3 threads were requested
ORACLE:  No application input on number of threads.
ORACLE:  Thread 1 contains 1 obs.
ORACLE:  Thread 2 contains 0 obs.
ORACLE:  Thread 3 contains 2 obs.
ORACLE:  Threaded read enabled. Number of threads created: 3
NOTE: There were 3 observations read from the data set MYDBLIB.SNOW_BaaaaaAYS.
NOTE: PROCEDURE PRINT used (Total process time):
       real time           1.12 seconds
       cpu time            0.17 seconds 
```