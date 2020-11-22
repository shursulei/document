# CLIENT_ENCODING=  LIBNAME Option

Specifies the encoding to use when transferring data from your DBMS.

| Valid in:                                                    | SAS/ACCESS LIBNAME statement                                 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Category:                                                    | Data Access                                                  |
| Default:                                                     | current SAS session encoding                                 |
| Restriction:                                                 | This option applies only to UNIX environments.               |
| Interaction:                                                 | This option overrides any value that you might have set for the PGCLIENTENCODING environment variable. |
| Data source:                                                 | PostgreSQL                                                   |
| Note:                                                        | Support for this option was added in SAS Viya 3.4.           |
| Examples:                                                    | Set the CLIENT_ENCODING= LIBNAME option:`libname mydb postgres server=myserver port=5432      user=myuser password='mypwd' database=mydb1 client_encoding='utf-8';` |
| Specify the PGCLIENTENCODING environment variable on PC hosts:`PGCLIENTENCODING UTF-8` |                                                              |
| Specify the PGCLIENTENCODING environment variable on UNIX hosts:`export PGCLIENTENCODING=UTF-8` | 配置在/xx/xx/SASHome/SASFoundation/9.4/bin/sas_env_local     |
| Specify the PGCLIENTENCODING environment variable at SAS invocation:`sas -set PGCLIENTENCODING=UTF-8` |                                                              |

Table of Contents

[Syntax](https://documentation.sas.com/#n0g4es203ifj9bn1h9q1ftrdmu2d)

[Syntax Description](https://documentation.sas.com/#p1q4zwml088zn8n1pfs71yp5qsra)

[Details](https://documentation.sas.com/#p0nybvzqjtlpnen1nkkvpz6mh2ae)

## Syntax

CLIENT_ENCODING='*encoding*'

### Syntax Description

#### *encoding*

specifies the encoding to use when transferring data into SAS.