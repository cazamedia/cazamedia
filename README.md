### Keep on keeping on...

+ PWAs - Progressive Web Applications - are an interesting way of delivering applications to end users.  Tis rather unfortunate that the *Firefox* and *Safari* web browsers do not support them and that Apple has intentionally hamstrung them to protect their *Walled Garden*.
  + Trying a PWA sample on my own system, I was happy to note that it worked straight away.  NOTE: My daily driver is LinuxMint and *Firefox* which, as mentioned above, does ***NOT*** support PWAs.  Consequently, I had to use Chromium.  Using Chromium, PWA installation was easy and I was able to install a PWA on my home screen without issue.
  + Additionally, I was able to modify the PWA on the host website and automagically have my changes propagate to the one installed on my desktop home screen on the next run. Good 😄!
  + I also tested this on my Android cellphone.  I learned that the *Brave* web browser also does not seem to like PWAs.  Chrome, however, worked fine.
  + As expected, installing this simple PWA on iOS - in this case my iPad - proved to be a painful experience.  I did, in point of fact, get it working after installing and using Google Chrome on iPad.  But I'm not sure it's something I could expect users to be able to achieve.
+ Python offers a large selection of ways to create desktop Graphical User Interfaces: Tkinter(CustomTkinter), Kivy, PyQt, wxPython, et cetera.
  + Tkinter is based on Tcl/Tk and is a very mature offering.  However, some consider that GUIs made with it look dated and unappealing in comparison to modern web & mobile applications.
    + Tkinter is licensed under the [Tcl/Tk License](https://www.tcl.tk/software/tcltk/license.html), a BSD-style license, for the Tcl/Tk porition and the Python license for the Python porition.
  + Kivy is a newer Python cross-platform GUI framework that can be utilized to make more modern GUIs in comparision to TKinter.
    + Kivy is MIT licensed and is 100% free to use for individuals and businesses with no strings attached.
    + Kivy supports Windows, Linux, macOS, Android, and iOS.
+ If you're using a SQLite database, you should almost always do a ***PRAGMA integrity_check*** on startup to verify that the database has not been corrupted.
+ SQLite supports in memory databases which, optionally, may be saved and loaded from persistent storage.  (Don't forget the integrity check on load as per above!)
+ PostgreSQL, at this time, does not appear to support *memory*-only database tables.  There are various approaches to tackle this limitation but an easy one appears to be to simply use a CTE (*Common Table Expression*) with data obtained from the desired source.  For example:
  
      WITH query_src AS (*get some data*) SELECT *desired_results* FROM query_src;
  
  + The ***get some data*** source could be provided by *memcached*, *KeyDB*, *Garnet*, a web service, a daemon internally using a SQLite *memory* table, et cetera. Use your imagination 😄!
  + Note that if you need to perform multiple queries on the source data, you should use the CTE to save the data to a Postgres *Temporary Table* and then proceed with the multiple queries.  Assuming, of course, that you cannot achieve the desired results in one single gigantic CTE 😄.
+ Note that using the above functionality in PostgreSQL, possibly in conjunction with Postgres' SQL/MED(*Management of External Data*) features, virtually any source that provides data in row-column format can be accessed via Postgres as if it were a database table and queries run against it.
  + *Common Table Expressions* in Postgres are incredibly powerful.  When combined with the concept that *everything's a table*, that's ***Next-Level*** technolology and potentially a ***Game Changer!***

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
