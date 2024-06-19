### I'd like to give a Shout Out to [Dr. Richard Hipp](https://en.wikipedia.org/wiki/D._Richard_Hipp), inventor of SQLite. Truly, an Unsung Hero ❤️🙏🙏🙏.

+ Python offers a large [selection](https://wiki.python.org/moin/GuiProgramming) of ways to create desktop Graphical User Interfaces: Tkinter(CustomTkinter), Tkinter(ttkbootstrap), Kivy, PyQt, PySide, wxPython, et cetera.
  + Tkinter is based on Tcl/Tk and is a very mature offering.  However, some consider that GUIs made with it look dated and unappealing in comparison to modern web & mobile applications.  If a more modern look is desired, there is the [ttkbootstrap](https://ttkbootstrap.readthedocs.io/en/latest/) project, a relatively new supercharged theme extension for Tkinter, that is MIT licensed, and that can provide an updated look and feel.
    + Tkinter is licensed under the [Tcl/Tk License](https://www.tcl.tk/software/tcltk/license.html), a BSD-style license, for the Tcl/Tk portion and the Python license for the Python portion.
  + Kivy is a newer Python cross-platform GUI framework that can be utilized to make more modern GUIs in comparision to TKinter.
    + Kivy is MIT licensed and is 100% free to use for individuals and businesses with no strings attached.
    + Kivy supports Windows, Linux, macOS, Android, and iOS.
  + PySide (and the original PyQt) are also Python cross-platform GUI frameworks based on Qt and recommended by a number of developers if the intent is to make a large-scale and/or complex GUI.
+ If you're using a SQLite database, you should almost always do a [***PRAGMA integrity_check***](https://www.sqlite.org/pragma.html#pragma_integrity_check) on startup to verify that the database has not been corrupted.
  + Optionally, use [***PRAGMA quick_check***](https://www.sqlite.org/pragma.html#pragma_quick_check) instead.
  + SQLite can get corrupted in the typical ways any file can get corrupted.
  + Consider using a file system that utilizes *Copy-On-Write* like ZFS.
  + Enabling *Write-Ahead-Logging* will minimize the window of when database corruption can occur as the only failure period is during a *checkpoint* operation.
    + *Write-Ahead-Logging* also boosts the speed of SQLite because data only needs to be written once, not twice like when a *rollback journal* is used.
    + *Write-Ahead-Logging* uses shared memory to speed-up searches; consequently, you cannot enable *WAL*-mode when the SQLite database is located on a network file-system.
    + *Write-Ahead-Logging* permits simultaneous readers and writers. This is permitted because changes do not overwrite the original database file but instead go into the separate write-ahead log file.
    + *Write-Ahead-Logging* is safe from corruption when ***PRAGMA synchronous=NORMAL*** is used, but WAL does lose durability. Consequently, a transaction committed in WAL mode with synchronous=NORMAL might roll back following a power loss or system crash.  Typically, this behavior is acceptable such situations so use of *synchronous=NORMAL* is a good choice.
    + Adjust [***PRAGMA wal_autocheckpoint***](https://www.sqlite.org/pragma.html#pragma_wal_autocheckpoint) setting as needed to optimize performance yet maintain reliability.
    + Use [***PRAGMA wal_checkpoint***](https://www.sqlite.org/pragma.html#pragma_wal_checkpoint) setting as needed to trigger a checkpoint.  There are a number of options available depending on your specific requirements.
  + SQLite does not natively support incremental database backups - ie. any changes to the database since the last backup. To achieve this functionality, you'll have to create it yourself.
  + Make sure that you properly enable *FOREIGN KEY* support in SQLite using ***PRAGMA foreign_keys=ON***; otherwise, you could inadvertently corrupt your data.
  + **IMPORTANT**: SQLite Isolation levels do not apply to queries executed on the same connection!  In other words, there's no isolation in that case.
+ **IMPORTANT**: SQLite, for legacy reasons, supports database tables with an inherent *rowid* as well as tables created ***WITHOUT ROWID***.  In certain cases, there are benefits to using the latter but there are [caveats](https://www.sqlite.org/withoutrowid.html#when_to_use_without_rowid).  Choose carefully 🤔.
  + This is yet another case where *legacy* code **bites**.  When laying the foundation of any software system and/or suite, care and attention ***SHOULD/MUST*** be devoted to ensure solid footing.  Otherwise, the consequences are dire.
  + **IMPORTANT**: Use [***PRAGMA index_info*** ](https://www.sqlite.org/pragma.html#pragma_index_info) to unambigously determine whether or not a SQLite database table is a ***WITHOUT ROWID*** table.  A normal SQLite database table will not return any rows but a *WITHOUT ROWID* table will return one or more rows.
+ SQLite supports in memory databases which, optionally, may be saved and loaded from persistent storage.  (Don't forget the integrity check on load as per above!)
  + SQLite supports CTEs(*Common Table Expressions*) as well as *user defined functions*.  Combining these can essentially enable something that behaves as if ***SQL/MED*** was in play. For example, create a user defined function that brings CSV data from a file or JSON data from a REST API or query results from a MongoDB database that can then be processed by SQLite's query parser. See below regarding PostgreSQL and CTEs for additional info.
    + NOTE: SQLite also has [virtual table](https://www.sqlite.org/vtab.html) support which can be utilized in a manner similar to the above and which may already have the desired [functionality](https://www.sqlite.org/vtablist.html) available.
+ SQLite does not support as many data types as PostgreSQL nor is it as strict.  However, if, like me, you've grown accustomed to the broad spectrum of PostgreSQL data types, consider using Python.  Python's [pickle](https://docs.python.org/3/library/pickle.html) module along with the Python SQLite3 module's [converter](https://docs.python.org/3/library/sqlite3.html#sqlite3.PARSE_DECLTYPES) functionality can essentially enable support for additional types equivalent to many of those in Postgres like UUIDs, timestamptz, hstore, etc.
  + NOTE: There is a [C extension for SQLite3](https://sqlite.org/src/file/ext/misc/uuid.c) that can be used to generate version 4 UUIDs.
+ TIP: If using SQLite with Python, be aware that in Python 3.12 they deprecated a few of the default [adapters and converters](https://docs.python.org/3/library/sqlite3.html#default-adapters-and-converters-deprecated).
  + Recommended practice is to use the [Adapter & Converter Recipes](https://docs.python.org/3/library/sqlite3.html#sqlite3-adapter-converter-recipes) tailored to your specific needs.
+ TIP: SQLite is multi-purpose and can be utilized as an [application file format](https://www.sqlite.org/draft/aff_short.html), for [automatic undo/redo](https://www2.sqlite.org/undoredo.html), and for [archive files](https://www.sqlite.org/sqlar.html) to name but a few use cases.
+ SQLite may also be used for [federated databases](https://fedjax.readthedocs.io/en/latest/_modules/fedjax/core/sqlite_federated_data.html).  See [tutorial](https://fedjax.readthedocs.io/en/latest/notebooks/dataset_tutorial.html) for more information.
+ When issuing DML queries to SQL databases, it is often desirable to keep a history of changes for auditing and/or logging purposes.  This is typically done using *trigger* functions.  The [sqlite-history](https://github.com/simonw/sqlite-history) Python module can facilitate this endeavor.
+ [Redka](https://github.com/nalgeon/redka) is a *redis*-like clone underpinned by the SQLite database and offers some interesting potential.
  + **IMPORTANT:** *Redka* is in a very early stage of development at present and there's no telling when it'll be ready for usage in production systems. 
+ PostgreSQL, at this time, does not appear to support *memory*-only database tables.  There are various approaches to tackle this limitation but an easy one appears to be to simply use a CTE (*Common Table Expression*) with data obtained from the desired source.  For example:
  
      WITH query_src AS (*get some data*) SELECT *desired_results* FROM query_src;
  
  + The ***get some data*** source could be provided by *memcached*, *KeyDB*, *Garnet*, a web service, a daemon internally using a SQLite *memory* table, et cetera. Use your imagination 😄!
  + Note that if you need to perform multiple queries on the source data, you should use the CTE to save the data to a Postgres *Temporary Table* and then proceed with the multiple queries.  Assuming, of course, that you cannot achieve the desired results in one single gigantic CTE 😄.
+ Note that using the above functionality in PostgreSQL, possibly in conjunction with Postgres' **SQL/MED**(*Management of External Data*) features, virtually any source that provides data in row-column format can be accessed via Postgres as if it were a database table and queries run against it.
  + *Common Table Expressions* in Postgres (and SQLite) are incredibly powerful.  When combined with the concept that *everything's a table*, that's ***Next-Level*** technolology and potentially a ***Game Changer!***
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
