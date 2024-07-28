### I'd like to give a Shout Out to [Dr. Richard Hipp](https://en.wikipedia.org/wiki/D._Richard_Hipp), inventor of SQLite. Truly, an Unsung Hero ❤️🙏🙏🙏.

+ Python offers a large [selection](https://wiki.python.org/moin/GuiProgramming) of ways to create desktop Graphical User Interfaces: Tkinter(CustomTkinter), Tkinter(ttkbootstrap), Kivy, PyQt, PySide, wxPython, et cetera.
  + Tkinter is based on Tcl/Tk and is a very mature offering.  However, some consider that GUIs made with it look dated and unappealing in comparison to modern web & mobile applications.  If a more modern look is desired, there is the [ttkbootstrap](https://ttkbootstrap.readthedocs.io/en/latest/) project, a relatively new supercharged theme extension for Tkinter, that is MIT licensed, and that can provide an updated look and feel.
    + Tkinter is licensed under the [Tcl/Tk License](https://www.tcl.tk/software/tcltk/license.html), a BSD-style license, for the Tcl/Tk portion and the Python license for the Python portion.
  + Kivy is a newer Python cross-platform GUI framework that can be utilized to make more modern GUIs in comparision to TKinter.
    + Kivy is MIT licensed and is 100% free to use for individuals and businesses with no strings attached.
    + Kivy supports Windows, Linux, macOS, Android, and iOS.
  + PySide (and the original PyQt) are also Python cross-platform GUI frameworks based on Qt and recommended by a number of developers if the intent is to make a large-scale and/or complex GUI.
+ SQLite is *the* most used database in history: it's ubiquitous.  Many [Distinctive Features](https://www.sqlite.org/different.html) set it appart from other SQL databases.
+ If you're using a SQLite database, you should almost always do a [***PRAGMA integrity_check***](https://www.sqlite.org/pragma.html#pragma_integrity_check) on startup to verify that the database has not been corrupted.
  + Optionally, use [***PRAGMA quick_check***](https://www.sqlite.org/pragma.html#pragma_quick_check) instead.
  + SQLite can get corrupted in the typical ways any file can get corrupted.
  + Consider using a file system that utilizes *Copy-On-Write* like ZFS.
  + Enabling *Write-Ahead-Logging* will minimize the window of when database corruption can occur as the only failure period is during a *checkpoint* operation.
    + *Write-Ahead-Logging* also boosts the speed of SQLite because data only needs to be written once, not twice like when a *rollback journal* is used.
    + *Write-Ahead-Logging* uses shared memory to speed-up searches; consequently, you cannot enable *WAL*-mode when the SQLite database is located on a network file-system.
    + *Write-Ahead-Logging* permits simultaneous readers along with a single writer. This is permitted because changes do not overwrite the original database file but instead go into a separate write-ahead log file or [two](https://www.sqlite.org/cgi/src/doc/wal2/doc/wal2.md).
      + In a future release of SQLite, a new transaction mode [***BEGIN CONCURRENT***](https://www.sqlite.org/cgi/src/doc/begin-concurrent/doc/begin_concurrent.md) will allow multiple simultaneous writers under certain conditions and subject to certain limitations/restrictions.
    + *Write-Ahead-Logging* is safe from corruption when ***PRAGMA synchronous=NORMAL*** is used, but WAL does lose durability. Consequently, a transaction committed in WAL mode with synchronous=NORMAL might roll back following a power loss or system crash.  Typically, this behavior is acceptable in such situations so use of *synchronous=NORMAL* is a good choice.
    + Adjust [***PRAGMA wal_autocheckpoint***](https://www.sqlite.org/pragma.html#pragma_wal_autocheckpoint) setting as needed to optimize performance yet maintain reliability.
    + Use [***PRAGMA wal_checkpoint***](https://www.sqlite.org/pragma.html#pragma_wal_checkpoint) setting as needed to trigger a checkpoint.  There are a number of options available depending on your specific requirements.
      + While not yet released, [***wal2***](https://www.sqlite.org/cgi/src/doc/wal2/doc/wal2.md) journal_mode is coming to SQLite which should resolve some issues betwixt wal files and checkpoints under certain conditions.
  + SQLite does not natively support incremental database backups - ie. any changes to the database since the last backup. To achieve this functionality, you'll have to create it yourself.
  + Make sure that you properly enable *FOREIGN KEY* support in SQLite using ***PRAGMA foreign_keys=ON***; otherwise, you could inadvertently corrupt your data.
  + **IMPORTANT**: SQLite Isolation levels do not apply to queries executed on the same connection!  In other words, there's no isolation in that case.
+ **IMPORTANT**: SQLite, for legacy reasons, supports database tables with an inherent *rowid* as well as tables created ***WITHOUT ROWID***.  In certain cases, there are benefits to using the latter but there are [caveats](https://www.sqlite.org/withoutrowid.html#when_to_use_without_rowid).  Choose carefully 🤔.
  + This is yet another case where *legacy* code **bites**.  When laying the foundation of any software system and/or suite, care and attention ***SHOULD/MUST*** be devoted to ensure solid footing.  Otherwise, the consequences are dire.
  + **IMPORTANT**: Use [***PRAGMA index_info*** ](https://www.sqlite.org/pragma.html#pragma_index_info) to unambigously determine whether or not a SQLite database table is a ***WITHOUT ROWID*** table.  A normal SQLite database table will not return any rows but a *WITHOUT ROWID* table will return one or more rows.
+ SQLite supports [in memory](https://www.sqlite.org/inmemorydb.html) databases which, optionally, may be saved and loaded from persistent storage.  (Don't forget the integrity check on load as per above!)
  + SQLite supports CTEs(*Common Table Expressions*) as well as *user defined functions*.  Combining these can essentially enable something that behaves as if ***SQL/MED*** was in play. For example, create a user defined function that brings CSV data from a file or JSON data from a REST API or query results from a MongoDB database that can then be processed by SQLite's query parser. See below regarding PostgreSQL and CTEs for additional info.
    + **IMPORTANT**: There are [Security Implications](https://www.sqlite.org/appfunc.html#security_implications) when using *user defined functions*: take heed!
    + NOTE: SQLite also has [virtual table](https://www.sqlite.org/vtab.html) support which can be utilized in a manner similar to the above and which may already have the desired [functionality](https://www.sqlite.org/vtablist.html) available.
      + **IMPORTANT**: If you're using Python's SQLite module, by default it does not allow SQLite extensions to be utilized for security reasons. Consequently, a bespoke compiled version of Python with *PY_SQLITE_ENABLE_LOAD_EXTENSION* defined is required if you want to use this functionality. This is handled by invoking the Python *configure* build configuration script as follows:

            configure --enable-loadable-sqlite-extensions

      + Note that it might be tempting to try to modify the sqlite source code to automatically include various extensions like the *csv* extension; however, doing so is limiting, especially when 3rd party extensions offer some intriguing possibilities.  For example, the [**sqlite-http**](https://github.com/asg017/sqlite-http) extension is written in Go and essentially web-enables SQLite.
      + *Alternative*: Use the Python [**sqlite-vtfunc**](https://github.com/coleifer/sqlite-vtfunc) package to achieve the desired SQLite *virtual table* functionality by using Python-based table functions.
        + TIP: [**sqlite-vtfunc**](https://github.com/coleifer/sqlite-vtfunc) package has example ***GenerateSeries*** which can be used to generate *series* data without having to load the [*generate_series*](https://www.sqlite.org/series.html) table-valued function.
        + TIP: See [article](https://charlesleifer.com/blog/sqlite-table-valued-functions-with-python/) for more details including interesting examples.
      + *Alternative*: Use the Python [**sqlean**](https://github.com/nalgeon/sqlean.py) package which is used as a replacement for the Python sqlite package and has a large number of extensions built-in with extensions enabled by default.  This is arguably an easier route to use [virtual_tables](https://www.sqlite.org/vtab.html).
+ Using an ORM like [**peewee**](https://github.com/coleifer/peewee) makes working with both SQLite and PostgreSQL databases easier. Additionally, a lot of enhanced functionality is provided to extend the capabilities of each database. Also consider using [**SQLAlchemy**](https://www.sqlalchemy.org/) which is the defacto Python-based ORM and highly prized by many developers.
+ **IMPORTANT**: SQLite does not support the SQL *TRUNCATE* statement.  Use 'DELETE FROM *table_name*' to truncate the specified table, but be aware of the following:
  + If the truncated table has specifically defined an *autoincrement* column as the *primary key*, then to reset the sequence you have to manually delete the entry from the *sqlite_sequence* table - eg. DELETE FROM sqlite_sequence WHERE name=*table_name*;  Otherwise, it will continue using the previous sequence.
  + If the truncated table is a *WITH ROWID* table but does not specifically indicate the primary key column as an *autoincrement* column, SQLite will automatically reset the sequence: no manual reset is required.
  + To summarize the behavior when a SQLite database table is *truncated*:
    + *CREATE TABLE test( id INTEGER PRIMARY KEY AUTOINCREMENT, data TEXT );*  Requires manual *DELETE FROM sqlite_sequence WHERE name='test';*
    + *CREATE TABLE test( id INTEGER PRIMARY KEY, data TEXT );*  SQLite will automatically reset the sequence for the *id* column.
    + *CREATE TABLE test( data TEXT );*  SQLite will automatically reset the sequence of the hidden ***rowid*** column.
+ SQLite only allows a single *autoincrement* column per table and only if the table is a *WITH ROWID* table.  SQLite ***does not*** support *autoincrement* columns on *WITHOUT ROWID* tables. Use *trigger* functions if you need to support bespoke *autoincrement* columns. 
+ SQLite does not support as many data types as PostgreSQL nor is it as [strict](https://www.sqlite.org/different.html#typing).  However, if, like me, you've grown accustomed to the broad spectrum of PostgreSQL data types, consider using Python.  Python's [pickle](https://docs.python.org/3/library/pickle.html) module along with the Python SQLite3 module's [converter](https://docs.python.org/3/library/sqlite3.html#sqlite3.PARSE_DECLTYPES) functionality can essentially enable support for additional types equivalent to many of those in Postgres like UUIDs, timestamptz, hstore, etc.
  + NOTE: There is a [C extension for SQLite3](https://sqlite.org/src/file/ext/misc/uuid.c) that can be used to generate version 4 UUIDs.
  + TIP: The PostgreSQL *citext* extension enables a *case-insensitive text* data type.  In SQLite, use *TEXT COLLATE NOCASE* for the data type to obtain similar behavior.
  + **IMPORTANT**: SQLite does not have native *datetime* or *timestamp* data types like PostgreSQL which can lead to issues.  The SQLite [documentation](https://www.sqlite.org/lang_datefunc.html) helps but be careful of potential [caveats/limitations](https://www.sqlite.org/lang_datefunc.html#caveats_and_bugs).
    + If the native *datetime* / *timestamp* handling is not to your liking, one possibility is to use fixed-point math in conjunction with the Unix Epoch.
      + Using a maximum time of ***9223372035*** seconds since the Unix Epoch equates to the date: ***Fri Apr 11 23:47:15 2262 UTC***
      + A 6-digit fixed point to handle microseconds resolution of the timestamp: ***9223372035000000***
      + A 9-digit fixed point to handle nanoseconds resolution of the timestamp: ***9223372035000000000***
      + Either of these choices is less than an 64-bit signed integer maximum: ***9223372036854775807***
      + IMPORTANT: Goal is to utilize native SQLite 64-bit signed integer handling for doing comparisons for sorting & searching without having to utilize any conversions.
      + IMPORTANT: If using Python, when handling dates and times with timezones, opt to use the standard ***zoneinfo*** package added in Python 3.9 instead of the older ***pytz*** package if possible.
      + RANT: I've been spoiled by Postgres 😕.  It would have been nice had the SQLite developers baked in the equivalent of the Postgres *timestamptz* data type rather than having to use lesser solutions.
    + Another possibility is to utilize *time-based* version 6 or version 7 UUIDs per [RFC9562](https://www.rfc-editor.org/rfc/rfc9562).  There are a [plethora](https://uuid7.com/) of reasons to use them.  Plus, it's a standard 😀!
      + TIP: Python package [***uuid6***](https://pypi.org/project/uuid6/) supports version 6, 7, and 8 UUIDs as defined in RFC9562.
      + Unlike Postgres where a TIMEUUID type could be created to handle the necessary operations, helper functions will be required in Python+SQLite in order to convert UUIDv6 and/or UUIDv7 to timestamps which are then stored in an auxiliary column using trigger functions.  The auxiliary timestamp column can be indexed for fast time-based range queries. This requires more space but has the benefit that the auxiliary timestamp column is human-readable.
      + NOTE: Combining the aforementioned approaches - ie. a 64-bit integer fixed point *timestamp* with a bespoke TIMEUUID column containing the 64-bit timestamp along with a 64-bit random component may be the optimum solution when using SQLite/libSQL/rqlite/mvSQLite.  Per [RFC9562 section 6.1](https://datatracker.ietf.org/doc/html/rfc9562#section-6.1), if custom timestamp handling is required a version 8 UUID should be utilized.  There are a plethora of possible directions to go with this so chose wisely.
+ TIP: A *WITH ROWID* table in SQLite will randomly generate a new ***rowid***, instead of a monotonically increasing one, if there is already an existing record with the maximum rowid of *9223372036854775807*.

      CREATE TABLE random_rowid( id INTEGER PRIMARY KEY, info TEXT );
      INSERT INTO random_rowid(id) VALUES('9223372036854775807');
      INSERT INTO random_rowid(info) VALUES('Testing...'),('Testing...'),('Testing...');
      SELECT * FROM random_rowid;

+ TIP: SQLite does not directly support the use of *user-defined functions* for indexing data **but** *user-defined functions* can be utilized by SQLite *trigger functions*.  Using trigger functions to insert & update data in a separate timestamp column is an avenue to overcome this limitation.  Alternatively, it should be possible to (ab)use SQLite's *virtual table* functionality to essentially achieve the desired behavior which may offer other advantages.
  + **IMPORTANT**: Trigger functions that call user-defined functions will cause issues if the SQLite database file is modified using an external applicaton like SQLiteStudio or the sqlite command-line utility that do not have the *UDF* defined.
+ TIP: The [**sqlite-utils**](https://github.com/simonw/sqlite-utils) project has some useful functionality to facilitate working with SQLite databases.
+ TIP: In certain circumstances, it may be advantageous and/or necessary to store large files as BLOBs in a SQLite database.  However, doing so oft times requires that the entire file be loaded into memory which may cause issues.  In order to avoid this, the ***zeroblob()*** SQLite built-in function can be utilized to reserve space for the file then the BLOB object can be loaded in chunks.  Note that care must be taken to ensure that this activity happens within the confines of a single database transaction using BEGIN TRANSACTION...COMMIT. 
+ TIP: If using SQLite with Python, be aware that in Python 3.12 they deprecated a few of the default [adapters and converters](https://docs.python.org/3/library/sqlite3.html#default-adapters-and-converters-deprecated).
  + Recommended practice is to use the [Adapter & Converter Recipes](https://docs.python.org/3/library/sqlite3.html#sqlite3-adapter-converter-recipes) tailored to your specific needs.
+ TIP: SQLite's [**EXPLAIN QUERY PLAN**](https://sqlite.org/eqp.html) is extremely helpful in determining how the SQLite query planner is going to execute a query.  Use it to help tweak your SQL queries for optimal performance.  Use ***.eqp on*** in SQLite command line interface to automatically view query plans.
  + TIP: SQLite's query engine will *search* the index when locating either the *minimum* primary key or *maximum* primary key, but if you try to get both in a single query, SQLite will perform a table scan.
    + *SELECT min(id) FROM test;* -- Good - searches index. 
    + *SELECT max(id) FROM test;* -- Good - searches index.
    + *SELECT min(id),max(id) FROM test;* -- BAD - scans entire table 😕. 
      + Workaround: *SELECT (SELECT min(id) FROM test) min, (SELECT max(id) FROM test) max;* -- Better 😄
+ TIP: SQLite is multi-purpose and can be utilized as an [application file format](https://www.sqlite.org/draft/aff_short.html), for [automatic undo/redo](https://www2.sqlite.org/undoredo.html), and for [archive files](https://www.sqlite.org/sqlar.html) to name but a few use cases. Note that the SQLite database file format is [*platform agnostic*](https://www.sqlite.org/different.html#onefile).
+ SQLite may also be used for [federated databases](https://fedjax.readthedocs.io/en/latest/_modules/fedjax/core/sqlite_federated_data.html).  See [tutorial](https://fedjax.readthedocs.io/en/latest/notebooks/dataset_tutorial.html) for more information.
+ When issuing DML queries to SQL databases, it is often desirable to keep a history of changes for auditing and/or logging purposes.  This is typically done using *trigger* functions which opens up a world of possibilities.
+ SQLite is an embedded database - ie. it's ***embedded*** within an application.  In constrast, the PostgreSQL database uses a client/server model.  Both of these designs have various pros and cons.  If you want the benefits of the client/server model with SQLite, consider uing [***rqlite***](https://rqlite.io/) which is a lightweight, easy-to-use, distributed database based on SQLite.
+ [Redka](https://github.com/nalgeon/redka) is a *redis*-like clone underpinned by the SQLite database and offers some interesting potential.
  + **IMPORTANT:** *Redka* is in a very early stage of development at present and there's no telling when it'll be ready for usage in production systems. 
+ TIP: An excellent resource for SQLite that leads to a great many others is the [**awesome-sqlite repository**](https://github.com/planetopendata/awesome-sqlite).  Check it out 😄!
+ TIP: [**libSQL**](https://github.com/tursodatabase/libsql) is an open source, open contribution fork of SQLite that adds a lot extra functionality.  Super cool!  Check it out 😄!
+ TIP: [**mvSQLite**](https://github.com/losfair/mvsqlite) is essentially *Distributed MVCC SQLite* that runs atop *FoundationDB* 😄.
+ TIP: Do not be afraid to *pick-n-mix*. For example, it may be advantageous to have data in [***Protobuf***](https://github.com/protocolbuffers/protobuf) format stored in BLOB column in the database because most of the time you want or need the performance. Unfortunately, it's not human readable hence the need to *mix*.  Tis a relatively simple thing to have a data table that has associated trigger functions that then calll user-defined functions to update the *protobuf*-based entry or invalidate a cache or update an AWS S3 bucket, et cetera.  Endless possibilities 😄!
+ PostgreSQL, at this time, does not appear to support *memory*-only database tables.  There are various approaches to tackle this limitation but an easy one appears to be to simply use a CTE (*Common Table Expression*) with data obtained from the desired source.  For example:
  
      WITH query_src AS (*get some data*) SELECT *desired_results* FROM query_src;
  
  + The ***get some data*** source could be provided by *memcached*, *KeyDB*, *Garnet*, a web service (perhaps using [*pgsql-http*](https://github.com/pramsey/pgsql-http)), a daemon internally using a SQLite *memory* table, et cetera. Use your imagination 😄!
  + Note that if you need to perform multiple queries on the source data, you should use the CTE to save the data to a Postgres *Temporary Table* and then proceed with the multiple queries.  Assuming, of course, that you cannot achieve the desired results in one single gigantic CTE 😄.
+ Note that using the above functionality in PostgreSQL, possibly in conjunction with Postgres' **SQL/MED**(*Management of External Data*) features, virtually any source that provides data in row-column format can be accessed via Postgres as if it were a database table and queries run against it.
  + *Common Table Expressions* in Postgres (and SQLite) are incredibly powerful.  When combined with the concept that *everything's a table*, that's ***Next-Level*** technolology and potentially a ***Game Changer!***
  + PostgreSQL has a native *uuid* data type.  Unfortunately, at this time, it does not support version 7 or version 8 UUIDs (see [RFC9562](https://www.rfc-editor.org/rfc/rfc9562)).  Additionally, it would be nice to have version 7 UUIDs be indexed based on the internal timestamp in order to facilitate range-based queries which does not fit well with the existing type.  A *TIMEUUID*, similar to Apache Cassandra's, would be a useful addition.
+ ***IMPORTANT:*** If you're planning on using a swapfile on Linux and you're using ZFS, you must employ a special [procedure](https://forum.proxmox.com/threads/new-installation-system-raid1-how-to-create-swap.103157/).
  + Note that this is ***NOT RECOMMENDED***.  Especially for production systems due to an [open issue that can cause your machine to deadlock when low on memory](https://github.com/openzfs/zfs/issues/7734).
+ If you're involved in the development of a new hardware project *and* you're the person who's going to be developing the firmware/software that's going to run it, it is in your best interest to ensure that you're involved in the decision-making process when selecting the hardware that will be utilized.  Furthermore, if you do not activately participate in that process, you may find that others involved and that did participate made your work ***10 times*** more difficult if not outright impossible!
+ **AI** *is* beneficial; however, sometimes, it feels like an assistant that you have to micromanage 😕.

<!--
**cazamedia/cazamedia** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
