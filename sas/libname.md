# LIBNAME Statement Syntax for Relational Databases

Associates a SAS libref with a DBMS database, schema, server, or a group of tables and views.

| Valid in: | anywhere                                                     |
| --------- | ------------------------------------------------------------ |
| See:      | [Overview: LIBNAME Statement for Relational Databases](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n014tll0crgkwxn15c2h00ymk5kp.htm&docsetVersion=9.4&locale=en) |

Table of Contents

[Syntax](https://documentation.sas.com/#n0lzatdum3sx0wn1s2ffi7pjxd7p)

[Required Arguments](https://documentation.sas.com/#p0frsof49uymj2n1r34cwxun03tx)

[Optional Arguments](https://documentation.sas.com/#n1beuhkiu60tt9n1nz3wzh2n05ot)

[Details](https://documentation.sas.com/#p1jahvl79zqgyfn17wtap1t2hr3o)

[Form 1: Using Data from a DBMS](https://documentation.sas.com/#n12tzqh9kaummwn1ji8nntbni8dw)

[Form 2: Disassociating a Libref from a SAS Library](https://documentation.sas.com/#n1kcgaiwnjg8azn0zvgezv7cpds6)

[Form 3: Writing SAS Library Attributes to the SAS Log](https://documentation.sas.com/#p0ck3nup7rxdo1n1u1sspjebtnfy)

[SQL Views with Embedded LIBNAME Statements](https://documentation.sas.com/#p09f4gkmx50v58n11j8aq96t33dz)

[Assigning a Libref with a SAS/ACCESS LIBNAME Statement](https://documentation.sas.com/#p175exdg8aj5zxn1wwizduoy9tlt)

[Using the Prompting Window When Specifying LIBNAME Options](https://documentation.sas.com/#p1nxsdk36z1qkan11gaale37nuy0)

[Assigning a Libref to a Remote DBMS](https://documentation.sas.com/#n04cgs23i2tuf3n1igfouhrlnf8i)

[LIBNAME Options for Relational Databases](https://documentation.sas.com/#p07dc4di7ziuapn1wi5q4umfdbgs)

## Syntax

#### Form 1:

[LIBNAME ](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#n12tzqh9kaummwn1ji8nntbni8dw)*libref* *engine-name*
<*SAS/ACCESS-connection-options*>
<*SAS/ACCESS-LIBNAME-options*>;

#### Form 2:

[LIBNAME ](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#n1kcgaiwnjg8azn0zvgezv7cpds6)*libref* [CLEAR](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#n0sozt43e28uzdn1sxw0jjuoxvbq) | [_ALL_ CLEAR](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#n01xhrktudstcsn1c8jjf0yevo4d);

#### Form 3:

[LIBNAME ](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#p0ck3nup7rxdo1n1u1sspjebtnfy)*libref* [LIST](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#n1hwhpeoyz5i7dn12tgeseicb9eq) | [_ALL_ LIST](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#n01xhrktudstcsn1c8jjf0yevo4d);

### Required Arguments

#### *libref*

specifies any SAS name that serves as an alias to associate SAS with a [database](https://documentation.sas.com/), schema, [server](https://documentation.sas.com/), or group of tables and [views](https://documentation.sas.com/). Like the global SAS LIBNAME statement, the SAS/ACCESS LIBNAME statement creates shortcuts or nicknames for data storage locations. A *SAS* [libref](https://documentation.sas.com/) is an alias for a virtual or physical [directory](https://documentation.sas.com/). A *SAS/ACCESS* libref is an alias for the [DBMS](https://documentation.sas.com/) database, schema, or server where your tables and views are stored.

#### *engine-name*

specifies the SAS/ACCESS [engine](https://documentation.sas.com/) name for your DBMS, such as `hadoop`. The engine name is required. Because the SAS/ACCESS LIBNAME statement associates a libref with a SAS/ACCESS engine that supports connections to a particular DBMS, it requires a DBMS-specific engine name. See the DBMS-specific reference section for details.

#### CLEAR

disassociates one or more currently assigned librefs.

Specify *libref* to disassociate a single libref. Specify _ALL_ to disassociate all currently assigned librefs.

#### _ALL_

specifies that the CLEAR or LIST argument applies to all currently assigned librefs.

#### LIST

writes the attributes of one or more SAS/ACCESS libraries or [SAS libraries](https://documentation.sas.com/) to the SAS log.

Specify *libref* to list the attributes of a single SAS/ACCESS library or SAS library. Specify _ALL_ to list the attributes of all libraries that have librefs in your current session.

### Optional Arguments

#### *SAS/ACCESS-connection-options*

provide connection information and control how SAS manages the timing and concurrence of the connection to the DBMS. These arguments are different for each database. For example, to connect to a Hadoop database, your connection options are USER=, PASSWORD=, and SERVER=:

```
libname mydblib hadoop user=myusr1 password=mypwd1 server=hadoopsvr;
```

If the connection options contain characters that are not allowed in SAS names, enclose the values of the arguments in quotation marks. On some DBMSs, if you specify the appropriate system options or environment [variables](https://documentation.sas.com/) for your database, you can omit the connection options. For connection option details, see the DBMS-specific information for your SAS/ACCESS interface.

#### *SAS/ACCESS-LIBNAME-options*

define how DBMS [objects](https://documentation.sas.com/) are processed by SAS. Some LIBNAME options can enhance performance; others determine locking or naming behavior. For example, the PRESERVE_COL_NAMES= option lets you specify whether to preserve spaces, special characters, and mixed case in DBMS column names when creating tables. The availability and default behavior of many of these options are DBMS-specific. See the DBMS-specific reference section for LIBNAME options that are available for your SAS/ACCESS interface. For general information, see [LIBNAME Options for Relational Databases](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#p07dc4di7ziuapn1wi5q4umfdbgs).

## Details

[Form 1: Using Data from a DBMS](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#n12tzqh9kaummwn1ji8nntbni8dw)

[Form 2: Disassociating a Libref from a SAS Library](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#n1kcgaiwnjg8azn0zvgezv7cpds6)

[Form 3: Writing SAS Library Attributes to the SAS Log](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#p0ck3nup7rxdo1n1u1sspjebtnfy)

[SQL Views with Embedded LIBNAME Statements](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#p09f4gkmx50v58n11j8aq96t33dz)

[Assigning a Libref with a SAS/ACCESS LIBNAME Statement](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#p175exdg8aj5zxn1wwizduoy9tlt)

[Using the Prompting Window When Specifying LIBNAME Options](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#p1nxsdk36z1qkan11gaale37nuy0)

[Assigning a Libref to a Remote DBMS](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#n04cgs23i2tuf3n1igfouhrlnf8i)

[LIBNAME Options for Relational Databases](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en#p07dc4di7ziuapn1wi5q4umfdbgs)

### Form 1: Using Data from a DBMS

You can use a LIBNAME statement to read from and write to a DBMS table or view as if it were a [SAS data set](https://documentation.sas.com/).

For example, in MYDBLIB.EMPLOYEES_Q2, MYDBLIB is a SAS libref that points to a particular group of DBMS objects, and EMPLOYEES_Q2 is a DBMS table name. When you specify MYDBLIB.EMPLOYEES_Q2 in a DATA step or procedure, you dynamically access the DBMS table. SAS supports reading, updating, creating, and deleting DBMS tables dynamically.

### Form 2: Disassociating a Libref from a SAS Library

To disassociate or clear a libref from a DBMS, use a LIBNAME statement. Specify the libref (for example, MYDBLIB) and the CLEAR option as shown here:

```
libname mydblib CLEAR;
```

You can clear a single specified libref or all current librefs.

The database engine disconnects from the database and closes any free threads or resources that are associated with that libref's connection.

### Form 3: Writing SAS Library Attributes to the SAS Log

Use a LIBNAME statement to write the attributes of one or more SAS/ACCESS libraries or SAS libraries to the SAS log. Specify *libref* to list the attributes of a single SAS/ACCESS library or SAS library, as shown below.

```
libname mydblib LIST;
```

Specify _ALL_ to list the attributes of all libraries that have librefs in your current session.

### SQL Views with Embedded LIBNAME Statements

With SAS software, you can embed LIBNAME statements in the definition of an [SQL](https://documentation.sas.com/) view. This means that you can store a LIBNAME statement in an SQL view that contains all information that is required to connect to a DBMS. Whenever the SQL view is read, PROC SQL uses the embedded LIBNAME statement to assign a libref. After the view has been processed, PROC SQL unassigns the libref.

In this example, an SQL view of the Hadoop table Dept is created. Whenever you use this view in a SAS program, the Hadlib library is assigned. The library uses the connection information (user name, password, and data source) that is provided in the embedded LIBNAME statement.

```
proc sql;
    create view sasuser.myview as
       select dname from hadlib.dept
          using libname hadlib hadoop
                user=scott pw=tiger datasrc=hadsrv;
quit;
```

Note: You can use the USING LIBNAME syntax to embed LIBNAME statements in SQL views. For more information about the USING LIBNAME syntax, see the [SAS SQL Procedure User’s Guide](https://documentation.sas.com/?docsetId=sqlproc&docsetTarget=titlepage.htm&docsetVersion=9.4&locale=en).

### Assigning a Libref with a SAS/ACCESS LIBNAME Statement

This statement creates a libref, MYDBLIB, that uses the SAS/ACCESS Interface to Hadoop.

```
libname mydblib hadoop server=hadoopsvr user=user1 password=mypwd1;
```

The statement below associates the SAS libref MYDBLIB with a Hadoop database that uses the SQL*Net alias AIRDB_REMOTE. You specify the SCHEMA= option in the SAS/ACCESS LIBNAME statement to connect to the Hadoop schema in which the database resides. In this example, Hadoop schemas reside in a database.

```
libname mydblib hadoop user=myusr1 password=mypwd1
        server=hadoopsvr schema=hrdept;
```

The AIRDB_REMOTE database contains a number of DBMS objects, including several tables, such as STAFF. After you assign the libref, you can reference the table like a SAS data set and use it as a data source in any DATA step or SAS procedure. In the SQL procedure statement below, MYDBLIB.STAFF is the two-level SAS name for the STAFF table in the database AIRDB_REMOTE.

```
proc sql;
   select idnum, lname
      from mydblib.staff
      where state='NY'
      order by lname;
quit;
```

You can use the DBMS data to create a SAS data set.

```
data newds;
   set mydblib.staff(keep=idnum lname fname);
run;
```

You can also use the libref and data set with any other SAS procedure. This statement prints the information in the STAFF table.

```
proc print data=mydblib.staff;
run;
```

This statement lists the database objects in the MYDBLIB library.

```
proc datasets library=mydblib;
quit;
```

### Using the Prompting Window When Specifying LIBNAME Options

This statement uses the [DBPROMPT=](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=p1hord99hkuvsan1no6y5q6aiefe.htm&docsetVersion=9.4&locale=en) LIBNAME option to cause the DBMS connection prompting window to appear and prompt you for connection information.

Note: This information pertains to SAS windowing environment and is not applicable to SAS Viya.

```
libname mydblib oracle dbprompt=yes;
```

When you use this option, you enter connection information into the fields in the prompting window rather than in the LIBNAME statement.

You can add the [DEFER= ](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=p1aukl464makuln1mszw8l045xgy.htm&docsetVersion=9.4&locale=en)NO LIBNAME option to make the prompting window appear when the libref is assigned rather than when the table is opened.

```
libname mydblib oracle dbprompt=yes defer=no;
```

### Assigning a Libref to a Remote DBMS

SAS/CONNECT (single-user) and SAS/SHARE (multiple user) software give you access to data by means of *remote library services* (RLS). RLS lets you access your data on a remote machine as if it were local. For example, it permits a graphical interface to reside on the local machine. The data remains on the remote machine.

This access is given to data stored in many [types](https://documentation.sas.com/) of [SAS files](https://documentation.sas.com/). Examples include external databases (through the SAS/ACCESS LIBNAME statement and views that are created with it) and [SAS data views](https://documentation.sas.com/) (views that are created with PROC SQL, the DATA step, and SAS/ACCESS software). RLS lets you access SAS data sets, SAS views, and relational DBMS data that SAS/ACCESS  LIBNAME statements specify. For more information, see the discussion about remote library services in the [SAS/SHARE User’s Guide](https://documentation.sas.com/?docsetId=shrref&docsetTarget=titlepage.htm&docsetVersion=9.4&locale=en).

You can use RLS to update relational DBMS tables that are referenced with the SAS/ACCESS LIBNAME statement.

In the next example, the SAS/ACCESS LIBNAME statement makes a connection to a DB2 database that resides on the remote SAS/SHARE server REMOS390. This LIBNAME statement is submitted in a local SAS session. The SAS/ACCESS engine name is specified in the remote option RENGINE=. The DB2 connection option and any LIBNAME options are specified in the remote option ROPTIONS=. Options are separated by a blank space. RLSDB2.EMPLOYEES is a SAS data set that references the DB2 table EMPLOYEES.

```
libname rlsdb2 rengine=db2 server=remos390 
   roptions="ssid=db2a authid=testid";
proc print data=rlsdb2.employees;
run;
```

### LIBNAME Options for Relational Databases

When you specify an option in the LIBNAME statement, it applies to all objects (such as tables and views) in the database that the libref represents. For information about options that you specify on individual SAS data sets, see [About the Data Set Options for Relational Databases](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0xs7acasdxoggn1f6jie9fn15x4.htm&docsetVersion=9.4&locale=en). For general information, see [LIBNAME Statement Syntax for Relational Databases](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0v059azebcuepn1o8x4c7wi1pfs.htm&docsetVersion=9.4&locale=en).See the DBMS-specific reference section for LIBNAME options that are available for your SAS/ACCESS interface.

Many LIBNAME options are also available for use with the [SQL pass-through facility](https://documentation.sas.com/). See the section on the SQL pass-through facility in the documentation for your SAS/ACCESS interface to determine which LIBNAME options are available in the SQL pass-through facility for your DBMS. For general information, see [SQL Pass-Through Facility](https://documentation.sas.com/?docsetId=acreldb&docsetTarget=n0zrxodm0bij90n11r2fhkxrjit1.htm&docsetVersion=9.4&locale=en#n1ect0m5t6najgn1bl56zlrloty9).

When a like-named option is specified in both the LIBNAME statement and after a data set name, SAS uses the value that is specified on the data set name.