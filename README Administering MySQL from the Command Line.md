# cpanel-exams   Administering MySQL from the Command Line


MyISAM was the default until MySQL 5.5.5, when it was decided that the native storage engine default be changed to use InnoDB instead. 

MyISAM is faster in some basic-use circumstances and has the benefit of providing portable tables (meaning that table files can be moved around in the file system, even across databases, and their data will remain usable and intact), but InnoDB has more advanced features, such as row-level locking. Currently, MyISAM is restricted to table-level locking.

When discussing a table in a MySQL/MariaDB database, what other term is the word field synonymous with?
Acceptable responses: column, columns, a column



MyISAM

.MYD file: Stores the actual data from the table.
.MYI file: Stores the table’s indexes (if any exist).


InnoDB

.ibd file: Stores table's data and indexes.
ibdata1 file: Stored at the root of the data directory (/var/lib/mysql/). Contains system information, table metadata, data dictionary information, and various other InnoDB-specific components.

Independent Files
(Files that are created by MySQL, not a specific engine)

.frm file: Known as the format file. Contains structural and identification information on a table.
db.opt: Exists in the database folder to store database-wide configuration changes, like character sets.


Which of the two primary MySQL/MariaDB storage engines discussed above support row-level locking?

Acceptable responses: InnoDB



The data directory is the location that the MySQL data lives in. On cPanel servers, this defaults as the /var/lib/mysql directory. 

The .MYD files contain the data from the table, the .MYI files contain the indexes (if any), while the .frm files contain formatting information.

cPanel & WHM installations include the innodb_file_per_table option enabled by default. This setting instructs InnoDB to store each of its tablespaces into an individual table file, using .ibd as its extension.


If at all possible, you should avoid terminating  MySQL/MariaDB suddenly (non-gracefully), such as when using commands like kill -9.

This can cause database corruption in both MyISAM and InnoDB files. Instead, use this: 

/usr/local/cpanel/scripts/restartsrv_mysql --stop
Above: Using the restartsrv_mysql script with the --stop argument to gracefully attempt to stop the MySQL or MariaDB service.


What is the name of the configuration setting that cPanel & WHM environments add to the my.cnf file to change its native default behavior, instructing InnoDB to store each of its tablespaces in individual files, instead of lumping them all into the same data file?
Acceptable responses: innodb_file_per_table, innodb-file-per-table


Database prefixing, put simply, is the appending of a database owner's username before the name of the database, separated by an underscore ('_'). This was, at one time, a rudimentary way for us to handle database mapping, by simply identifying the database's owner based on the username that prefixed it.

user1_database1
user1_database2
user2_database1
user3_database1
user3_database2



You can rename the databases manually through the WHM » SQL Services » Manage Databases interface. We do not advise renaming databases from the command line. 

Should you need to manually associate a database with a user, this can be done through WHM, at the WHM » SQL Services » Database Map Tool interface. 

/usr/local/cpanel/bin/dbmaptool
Above: The command line utility found in /usr/local/cpanel/bin that can be used to manually perform database mapping operations, if absolutely necessary.


/var/cpanel/databases  

Within what full path in the file system does cPanel & WHM store its database mapping information?
Acceptable responses: /var/cpanel/databases


In a cPanel & WHM environment, MySQL/MariaDB's configuration file is found at /etc/my.cnf. 

That said, MySQL/MariaDB will check other locations, such as /, /usr, /etc, and /home. If you notice that, for some reason, MySQL/MariaDB is not using the values in /etc/my.cnf, it’s good to check for “sneaky” my.cnf files in those locations.  

The /root/.my.cnf (pay close attention to the preceding period - this is NOT the same as the my.cnf file) file is required for cPanel database functions to work properly and should not be removed, but if you find individual users with custom .my.cnf files it can be a useful test to move the file aside and then determine if that resolves the issue.

he /scripts/check_users_my_cnf script can be used to help find additional my.cnf files, in addition to checking the validity of user’s personal .my.cnf files. 

Remember... 
The .my.cnf and my.cnf files are two separate files. The only difference is the preceding period (.).
.my.cnf is intended to handle MySQL client behavior, while my.cnf is intended to handle MySQL server behavior, though each can still affect the other.

If /tmp does not have the proper permissions of 1777 (shown as drwxrwxrwt in ls -l output), then it can result in particularly large queries causing InnoDB to throw critical errors in your server, as they're unable to make appropriate use of temporary file storage as needed.

If InnoDB has been changed to behave as the default storage engine, and the permissions on /tmp are off, then MySQL/MariaDB may be unable to start successfully.


On a default cPanel & WHM installation, within which file would you find the root user's MySQL client settings, including the default MySQL host and root credentials?
Acceptable responses: /root/.my.cnf, .my.cnf, ~/.my.cnf




The following text is an example of which type of syntax, also found in some mapping-related files stored by cPanel in the file system?

MYSQL: 
  animals: 
    animals: 
      - GRANT USAGE ON *.* TO 'animals'@'localhost' IDENTIFIED BY PASSWORD '*75FE48658430441870C524D7ECB8F71EADFAEFD1'
      - GRANT ALL PRIVILEGES ON `birds`.* TO 'animals'@'localhost'
      - GRANT ALL PRIVILEGES ON `animals\_fish`.* TO 'animals'@'localhost'
PGSQL: {}

SQL
XML
****YAML
JSON


Which of the following statements about InnoDB tablespaces are true?


They can be stored individually within a database folder, or lumped together into the ibdata1 file.
They have a simple plain text files that are human-readable.
They can be safely relocated as needed via a simple mv command from the file system.
They all store a copy of the data dictionary containing descriptive information about the table's structure.


Which of the following file extensions are associated specifically with InnoDB tables?


.MYD
.MYI
.frm
**.ibd


The .frm and db.opt files will appear in database folders when using which of the following MySQL storage engines?


MyISAM
**Any storage engine.
InnoDB
XtraDB



In a cPanel &  WHM environment, entering the MySQL command line is simple...

Just use this command alone: mysql

/root/.my.cnf ile to log you into MySQL/MariaDB with the appropriate credentials. 

You can also override the automatic credentials or host by providing the user and password explicitly via the -u and -p flags, like this: 


mysql -u [username] -p

it's a good idea to ensure that these files never have anything less restrictive than 0600 permissions, and be owned by the user that holds the account.

There are a few common errors that can relate to both the /etc/my.cnf and the user .my.cnf files. 


ERROR 1045 (28000): Access denied for user 'something'@'localhost' (using password: YES)
SOLUTION

The first thing to try is to move the .my.cnf file elsewhere (or simply rename it), and then test once more to determine if that resolves the issue. 

If it does, you can reset the password through WHM and then update the .my.cnf file manually.


cars@cars.tld [~]# mysql
mysql: unknown variable 'char-set=utf-8'

o fix this issue as root, first, simply open /etc/my.cnf in an editor of your choice. Then, find the variable that was identified in the error message ('char-set' would be the problem variable, in our example above) and comment it out using the # symbol.  

If the error only appears when you’re entering MySQL as a specific user, then that's when it might be a good time to check for user-specific .my.cnf files and then, once found, either modify them (comment out the bad variable) or.. simply move it aside or rename the file.

What numeric permissions value do we recommend assigning a user-level .my.cnf file with, in order to prevent unauthorized access?

Acceptable responses: 0600, 600

SHOW VARIABLES;
Above: A simple SQL query to print all system variables and their values in MySQL. This will likely produce a lot of output on its own, so you'll generally want to filter it down, as described below.

in SQL syntax, the % characters act as wildcards. This means it's a special character that matches everything. Taking that into account, the above example query effectively reads:
The semicolon at the end of a statement tells MySQL that you’re done entering the command. Terminating your statement in some way is required for most commands on the MySQL command line.

In the field below, enter the query you would run if you wanted to show only variables that contain the word myisam in their name.
(Hint: Use a semicolon to terminate the statement.)
Acceptable responses: SHOW VARIABLES LIKE '%myisam%';, SHOW VARIABLES LIKE "%myisam";, SHOW VARIABLES LIKE '%myisam%'\G, SHOW VARIABLES LIKE "%myisam%"\G



If for any reason you don’t remember the exact name of the database you’re looking for, you can track it down using “SHOW DATABASES;”.

If prefixing is enabled, you can find databases owned by a particular user with a query similar to the following:

SHOW DATABASES LIKE “username_%”;  

In the field below, type the SQL statement needed to instruct MySQL to begin using a database named "vehicles".
(Again, make sure that you terminate the statement with a semicolon)
Acceptable responses: use vehicles;, use `vehicles`;, use 'vehicles';,

 get a list of all the tables in your current database, you can use the SHOW TABLES statement:

SHOW TABLES;


SHOW VARIABLES\G


In the field below, enter the query you would run if you wanted to show only variables that contain the word myisam in their name.
(Hint: Use a semicolon to terminate the statement.)

Acceptable responses: SHOW VARIABLES LIKE '%myisam%';, SHOW VARIABLES LIKE "%myisam";, SHOW VARIABLES LIKE '%myisam%'\G, SHOW VARIABLES LIKE "%myisam%"\G

SHOW DATABASES LIKE “username_%”;  



SHOW TABLES;
Above: A basic 'SHOW TABLES' statement to output all available tables in the currently used database. Just like the other statements we've looked at, this statement supports the use of a LIKE clause as well, so you can filter down the results to specifically find what you're looking for.



'SELECT [field] FROM [table];'

you might try something like 'SELECT last_name FROM employees;' to retrieve a list of last names stored in your 'employees' table. 

Let's say that we only want to see the first five results from our SELECT on the last_name field. To do this, we'd run the following:

SELECT last_name FROM employees LIMIT 5;


For instance, this query will give us all the last names that start with the letter 'A':

SELECT last_name FROM employees WHERE last_name LIKE “A%”;
Above: An example of a SELECT query using the WHERE ... LIKE syntax to identify specific entries from a field, based on criteria (in this case, all records starting with 'A'). Again, we use the wildcard character ('%') to achieve this result in our pattern.


The only table within this database that we’re specifically interested, in for the purposes of this course, is the “user” table.

This table contains information about, go figure, the users, which includes details such as each users' assigned privileges. Interestingly enough, the 'user' table in the 'mysql' database can be interacted with just like any other regular table you'd encounter in a MySQL database. 

The root user is missing or malformed
If root, or your primary administrator, doesn’t have those permissions, then resolving the issue requires that you actually stop MySQL and, instead of restarting it normally, you would need to start MySQL under a special mode allowing you to make changes even without having the necessary privileges.

Stop the MySQL Service
/scripts/restartsrv_mysql --stop

Start MySQL in skip-grant-tables Mode
mysqld_safe --skip-grant-tables --skip-networking &


use mysql;


SELECT * FROM user WHERE User=“root”;

What you should see returned from this are four different rows, each representing root access from a different "host" source:

localhost
127.0.0.1
::1
The server's hostname
For the root user, all columns that contain priv in the name (representing a particular privilege) should be set to Y, indicating that the user has that particular privilege enabled. 

/scripts/restartsrv_mysql


What is the name of the table that contains privilege and access information, stored within the system mysql database?
Acceptable responses: user

The mysqladmin command has quite a few useful options. The one used for monitoring MySQL is “process” (which can be shortened as far as simply using 'pr' to execute). This command shows you the same information shown in WHM » SQL Services » Show MySQL Processes.


There is also a top-like tool called mytop. It works just like the standard top utility, only by monitoring MySQL/MariaDB threads instead of processes. 

To install mytop use:

yum install mytop


To configure mytop you’ll need to create a /root/.mytop file. The only line it must have in it is “db=mysql”, though there are quite a few options you could potentially utilize here to add some additional features and customizations to mytop.

In MySQL/MariaDB, each connection is handled by its own ________.
Acceptable responses: Answer 1, thread


From the command line in a cPanel & WHM environment, how could you determine whether a server is utilizing a remote MySQL server?

Check /etc/my.cnf for a “remote-host” variable.
Check the MySQL RPMs to see if mysql-client is installed instead of mysql-server.
****Check /root/.my.cnf for a “host” line.
Check the process list. If mysqld is not listed, the server is set up for remote mysql.


Which of the following is the standard wildcard character used in SQL queries?

*
!
@
****%

Which of the following statements is not a legitimate SQL statement?

INSERT
REMOVE
UPDATE
****COPY


Which of the following SQL statements can be used to provide descriptive information about databases, tables, columns, variables, or status information about the server?

EXPLAIN
UPDATE
STATUS
****SHOW


Put simply, a database map is one method of associating users with databases (and vice versa). The map is essentially a data structure used to associate items with each other.

The database mapping information that cPanel utilizes is kept on-disk, located within the /var/cpanel/databases folder.  

This folder will potentially consist of:

1

A JSON file for each cPanel user that contains the list of databases belonging to the user.

2

A YAML file for each cPanel user that contains the grants that allow the database users associated with that cPanel user to access the databases and associated cache file.

3

A users.db file that lists host and what database users are associated with them and a user.db.cache file that contains the same information in a more compact format.

4

A dbindex.db.json file that contains a list of databases along with the cPanel user that is associated with it.


YAML and JSON files. The YAML files are easier to read

The JSON files are harder to read but can be formatted with a simple piped command.


cat user.json | python -mjson.tool
Above: Using the python -mjson.tool command to format JSON input in a more readable way. Another alternative to using python for this is to use the json_pp utility, which is often natively included in many distributions.

The user cpses_an68RkSa5m is a special user. It is created and destroyed automatically by cPanel. It allows access to phpMyAdmin without requiring a second login or storing the cPanel password in an unsecured format.

ls /usr/local/cpanel/bin

dbindex
dbmaptool
dbstoregrants
mysqluserstore
restoregrants
setupdbmap

When the dbindex.db.json file is incomplete, the dbindex script will not complete successfully. It will instead output an error stating that “the dbindex cache file is out of date”.

One of the best ways to resolve this issue is to restore a dbindex.db.json file from a backup. 

Another possible solution, though potentially tedious, is to move the file out of the way, then manually re-associate the databases with their cPanel accounts.


When looking at the following excerpt from the beginning of a SHOW GRANTS result, what do the wildcard asterisks represent in the *.* shown below?

GRANT USAGE ON *.*


[username].[hostname or IP]
***[database].[table]
[username].[groupname]
[hostname or IP].[database]


Of the following options, which would be the optimal way to resolve the issue that causes email notifications containing content similar that shown below?

dbindex cache file is out of date


***Restore the file from an available backup.
Manually edit the file to correct the corrupted data.
Delete the file and allow it to be recreated automatically.
Run /scripts/fix-dbindex.


Most of the executables found within the /usr/local/cpanel/bin directory are not intended for standard usage by users, and are not developed to be user-friendly (frequently lacking any kind of help output or formal documentation). These are intended primarily for back-end and automated handling, as opposed to those executables found within the /usr/local/scripts (/scripts) folder.

There is at least one notable exception to this. Which of the following tools found within this path is actually recommended for server administrator use as needed, in certain circumstances?


***dbmaptool
restoregrants
updatephpconf
setupnameserver


Which of the following WHM interfaces updates the files in the /var/cpanel/databases directory?

***Database Map Tool
phpMyAdmin
Manage MySQL® Profiles
MySQL® Root Password


cPanel provides a basic /etc/my.cnf file. It contains only five items:

[mysqld]
performance-schema=0
innodb_file_per_table=1
max_allowed_packet=268435456
open_files_limit=10000
default-storage-engine=MyISAM
Above: The /etc/my.cnf file as it appears by default on a fresh cPanel & WHM installation.


Above: The options in the Tweak Settings interface that control automatic management of values in the my.cnf file. Note the innodb_buffer_pool option can also be enabled for automatic management but is disabled by default. IF enabled, this value will be added to the my.cnf file with a value calculated based on your system's resource availability.

Another option that you'll notice is the innodb_file_per_table value. This configuration value instructs any newly created InnoDB tables to be stored as individual table files (ending in extension ibd) within their appropriate database folder. 

What MySQL/MariaDB storage engine is defined as the default on a standard cPanel & WHM installation?
Acceptable responses: MyISAM

If for any reason you find that your server seems to refuse to accept newly defined configuration changes, or appears to have customizations that are not in the /etc/my.cnf file, you may have another my.cnf file causing trouble. 

Typically, you'll most often find these "stray" my.cnf files stored in one of the following paths:

The Root Directory: /

/usr

/home


/usr/local/cpanel/scripts/check_users_my_cnf  




Migrating the MySQL/MariaDB Data Directory

If the /var partition approaches its space limitation, the best option is to create a new partition (frequently via the installation of a new disk) and mounting the new partition on /var/lib/mysql.


You may recall the details of the backup procedure from the second unit of this course. However, you may also use this quick command to store a backup of all of your databases into the /root/alldatabases.sql.gz file:

mysqldump --opt -AER | gzip > /root/alldatabases.sql.gz

You can stop MySQL with the following command:

/scripts/restartsrv_mysql --stop

Since you are creating a new partition, you’ll need to rename the data directory:

mv /var/lib/mysql /var/lib/mysql-backup

Next, you’ll need to edit /etc/fstab so that the new partition is mounted when the server is rebooted. For example, if sdc1 is your newly created partition, the line should look like this:

/dev/sdc1     /var/lib/mysql     ext3     defaults,usrquota     0 1
Above: An example of what the new fstab entry might look like, with a newly created sdc1 partition.

Next, you need to mount the new partition.

You can use the command “mount -a” to mount all the partitions in /etc/fstab. Once the partition is mounted, you will need to change the directory ownership to mysql:mysql and ensure it has a permissions value of 0711, using the chown and chmod commands.


The final step of this process is to actually restore the original databases. 

You can do this by copying (or simply renaming, though copying leaves you with a fallback plan) the original data directory (the one that we renamed in a previous step) back to /var/lib/mysql. 

With these actions completed, we can then turn MySQL back on (and re-enable monitoring), and begin working with our data from within the new partition.
Above: Renaming the default data directory path to a backup path name.

Make good use of the Slow Query Log

add the following values to your /etc/my.cnf file:



slow_query_log = 1
log-slow-queries = /var/log/mysql-slow.log
long_query_time = 2

NOTE: In MySQL 5.7, the variable log-slow-queries is deprecated. Use slow_query_log_file instead.

MyISAM files ARE portable; InnoDB files are NOT.


In which of the discussed MySQL/MariaDB storage engines are table files portable, in that they can be copied or moved around the file system and still function as tables when restored or placed within a database folder?

Acceptable responses: MyISAM

In MySQL 5.7, the log-slow-queries variable is deprecated. Which variable should you use instead, to define the destination path for the slow query log file?

Acceptable responses: slow_query_log_file


In MySQL version 5.6, what happens if an entry in /etc/my.cnf is an unrecognized variable?


MySQL starts up normally and ignores the bad variable.
MySQL starts up, but logs a warning about the variable.
***MySQL refuses to start and logs an error message.
MySQL appears to start normally, but aborts with no errors immediately.

Which of the following is closest described as the structural metadata used to track objects like tables, indexes, and columns when using the InnoDB storage engine?

Foreign keys
Information schema
***Data dictionary
Primary index

Error messages in your database error logs that indicate invalid, missing or unexpected system tables can frequently be the result of which of the following issues?


A missing storage engine plugin.
A missing data transaction log file.
An invalid my.cnf configuration value.
***An incomplete (partial) upgrade.

Which of the following MySQL/MariaDB-related variables can be controlled via an option found within WHM's Tweak Setting interface?

binlog_cache_size
innodb_file_per_table
***max_allowed_packet
default_character_set

If the MySQL processes are stopped using the kill -9 command, what can you expect to happen?


***The severed writes on the databases will increase risk of database corruption.
An increased security risk of compromised authentication.
The log files will produce an excessive number of warnings, which may begin causing disk space issues if unaddressed.
A DROP is automatically triggered by MySQL on the last table that was being written to at the time, to prevent damage to the system.

How should you enable the slow query log on a cPanel & WHM server?


Change the Tweak Setting for “Enable Slow Query Log” to “On”.
***Add "slow_query_log=1" to the mysqld section of the /etc/my.cnf configuration file.
Create the mysql directory in /var/log/ and add an empty "query_log" file.
Add "slow-log=/path/to/log" anywhere in the /etc/my.cnf configuration file.

By default, on a cPanel & WHM environment, you can find the MySQL logs in which of the following locations?


MySQL logs to /var/log/messages by default
***/var/lib/mysql
/var/cpanel/databases
/var/log/mysql

When should you make backups of your configuration files (or the my.cnf file in particular)?


***When changes are made to the configuration.
Extremely often.
Weekly.
Never. There’s no reason to make backups of configuration files because you can rebuild them.**


Which of the following pieces of information can you find in both the slow query log and the general query log?

The current information_schema memory usage.
The amount of time a lock was active.
***The user who executed the query.
How long it took the query to be executed.

In a cPanel & WHM environment, which of the following tools is best described as the command-line equivalent of WHM’s Database Map Tool interface? 

***dbmaptool
dbindex
setupdbmap
dbmapsetup

How can you prevent cPanel & WHM from performing automated updates on the MySQL® or MariaDB server software?

By changing the “Allow cPanel to update MySQL®/MariaDB” option, found in Tweak Settings.
cPanel does not perform automated updates on MySQL®/MariaDB.
By changing the settings found in the "Update" tab of the Manage MySQL® Profiles interface in WHM.
***By using the update_local_rpm_version script.


If the backup directory is /backup, where would you find the compressed backup file of the full MySQL data directory?

/backup/system/directories/[date]/mysql.tar.gz
/backup/cpanel/dirs/_mysql_.tar.gz
/backup/[date]/system/dirs/_var_lib_mysql.tar.gz
***/backup/system/[date]/_var_lib_mysql.tar.gz

Which of the following MySQL/MariaDB-related variables can be controlled via an option found within WHM's Tweak Setting interface?

***max_allowed_packet
default_character_set
innodb_file_per_table
binlog_cache_size

Which of the following MySQL logs are not recommended to leave enabled on a production server?

General query log: /var/lib/mysql/hostname.log
The InnoDB transaction logs: /var/lib/mysql/ib_logfile[0|1]
***Slow query log: /var/lib/mysql/slow-hostname.log
MySQL error log: /var/lib/mysql/hostname.err


Which of the following variables in a cPanel & WHM server’s /etc/my.cnf file are set by default?


innodb-file-per-table=0
default-storage-engine=MyISAM
default-character-set=utf-8
auto_increment_offset=2

Which of the following files should you check if you receive an unknown variable error message during MySQL operation or service start-up?


/var/cpanel/databases/mysql.conf
/var/cpanel/cpanel.config
/etc/my.cnf
/var/lib/mysql/mysql/db.opt


In a cPanel & WHM environment, which of the following tools' purpose can be described as the executable that is responsible for the initial setup of database mapping, as well as any updates that occur between versions?

***setupdbmap
dbstoregrants
dbindex
dbmaptool

In a cPanel & WHM environment, which of the following tools is best described as the command-line equivalent of WHM’s Database Map Tool interface?
***dbmaptool
setupdbmap
dbindex
dbmapsetup

LOGS

By default, the logs are found in the data directory (again: defaults to /var/lib/mysql), and the filename is the hostname of the server appended with .err.

less /var/lib/mysql/`hostname`.err
less /var/lib/mysql/$(hostname).err

The $() method in Bash is currently the officially recommended approach, though the older backticks (``) use is still typically supported on most systems.

The files named ib_logfile, followed by a number (typically ib_logfile0 and ib_logfile1, sometimes with ib_logfile101 as well) are not the MySQL/MariaDB logs but are instead specifically part of the InnoDB storage engine. 

If you’re running MariaDB, you may also see files that start with aria_log. These are for MariaDB's ARIA storage engine, and are also binary, meaning that they are not intended to be "human-readable" either. These serve a similar purpose to that of the ib_logfiles.

You can change this configuration from within the /etc/logrotate.d/mysql file, which provides instructions for the logrotate daemon, otherwise known as logrotated.

[hostname]-slow.log (where [hostname] is the local part of your hostname) and can be found in the standard MySQL/MariaDB data directory. You can enable it by adding slow_query_log=1 to the [mysqld] section of the /etc/my.cnf configuration file.

The general query log is enabled with the general_log=1 directive in the [mysqld] section of the /etc/my.cnf file. When enabled, it creates entries like the two shown below:

We do not advise keeping the general query log enabled on production servers, as it can grow very large, very quickly, and it can significantly impact your server's disk input/output performance

What is the default MySQL/MariaDB data directory?
Acceptable responses: /var/lib/mysql, /var/lib/mysql/

Before performing any repair or recovery procedures on your databases, make sure and take backups of both the physical data directory as well as the SQL data. 



Actual repairs involve changes to the data, which will always come with the potential for problems and a reasonable share of risk.

The REPAIR Statement
If you’re already within the MySQL/MariaDB command-line shell, you can utilize the REPAIR SQL statement from directly within MySQL:

mysql> REPAIR TABLE known_netblocks;

The mysqlcheck Utility
To perform repairs based on those checks, the -r or --repair flag should be added after mysqlcheck.

# mysqlcheck -r cphulkd


The myisamchk Utility

If MySQL/MariaDB is not running, you can use myisamchk to check one or more tables by specifying the actual table file to check. You can then run myisamchk —recover (or -r, for the short version). You can also use a wildcard; it’s not uncommon to run something like myisamchk /var/lib/mysql/eximstats/*.MYI to check all of the index table files in a particular database (eximstats being the name of the database in that particular example). 

Let's take a look at this in action:

# myisamchk --recover  /var/lib/mysql/eximstats/*.MYI

It is important that myisamchk only be run when MySQL/MariaDB is off, or it may result in further corruption. The myisamchk utility is considered an offline MySQL utility.

The myisamchk utility is considered an offline repair utility. What other command-line utility is considered an online repair utility for MyISAM, and can be performed while MySQL/MariaDB is running?
Acceptable responses: mysqlcheck, mysqlcheck -r, mysqlcheck --repair, mysqlcheck database


To save the server from corrupting its data any further, it will force MySQL to stop (AKA crash)
