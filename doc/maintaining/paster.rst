======================
Command Line Interface
======================

Most common CKAN administration tasks can be carried out from the command line
on the server that CKAN is installed on, using the ``paster`` command.

If you have trouble running paster commands, see
`Troubleshooting Paster Commands`_ below.

.. note::

   Before running a CKAN ``paster`` command, you have to activate your CKAN
   virtualenv and change to the ``ckan``  directory, for example:

   .. parsed-literal::

      |activate|
      cd |virtualenv|/src/ckan

   To run a paster command without activating the virtualenv first, you have
   to give the full path the paster script within the virtualenv, for example:

   .. parsed-literal::

      |virtualenv|/bin/paster --plugin=ckan user list -c |development.ini|

   To run a paster command without changing to the ckan directory first, add
   the ``--plugin=ckan`` option to the command. For example:

   .. parsed-literal::

      paster --plugin=ckan user list -c |development.ini|

   In the example commands below, we assume you're running the commands with
   your virtualenv activated and from your ckan directory.

The general form of a CKAN ``paster`` command is:

.. parsed-literal::

   paster **command** --config=\ |development.ini|

The ``--config`` option tells CKAN where to find your config file, which it
reads for example to know which database it should use. As you'll see in the
examples below, this option can be given as ``-c`` for short.

``command`` should be replaced with the name of the CKAN command that you wish
to execute. Most commands have their own subcommands and options. For example,
to print out a list of all of your CKAN site's users do:

.. parsed-literal::

   paster user list -c |development.ini|

(Here ``user`` is the name of the CKAN command you're running, and ``list`` is
a subcommand of ``user``.)

For a list of all available commands, simply run ``paster`` on its own with no
command, or see `Paster Commands Reference`_. In this case we don't need the
``-c`` option, since we're only asking CKAN to print out information about
commands, not to actually do anything with our CKAN site::

 paster

Each command has its own help text, which tells you what subcommands and
options it has (if any). To print out a command's help text, run the command
with the ``--help`` option::

   paster user --help


-------------------------------
Troubleshooting Paster Commands
-------------------------------

Permission Error
================

If you receive 'Permission Denied' error, try running paster with sudo.

.. parsed-literal::

  sudo |virtualenv|/bin/paster db clean -c |production.ini|

Virtualenv not activated, or not in ckan dir
============================================

Most errors with paster commands can be solved by remembering to **activate
your virtual environment** and **change to the ckan directory** before running
the command:

.. parsed-literal::

   |activate|
   cd |virtualenv|/src/ckan

Error messages such as the following are usually caused by forgetting to do
this:

* **Command 'foo' not known** (where *foo* is the name of the command you
  tried to run)
* **The program 'paster' is currently not installed**
* **Command not found: paster**
* **ImportError: No module named fanstatic** (or other ``ImportError``\ s)

Running paster commands provided by extensions
==============================================

**If you're trying to run a CKAN command provided by an extension** that you've
installed and you're getting an error like **Command 'foo' not known** even
though you've activated your virtualenv and changed to the ckan directory, this
is because you need to run the extension's paster commands from the extension's
source directory not CKAN's source directory. For example:

.. parsed-literal::

   |activate|
   cd |virtualenv|/src/ckanext-spatial
   paster foo -c |development.ini|

This should not be necessary when using the pre-installed extensions that come
with CKAN.

Alternatively, you can give the extension's name using the ``--plugin`` option,
for example

.. parsed-literal::

   paster --plugin=ckanext-foo foo -c |development.ini|

.. todo::

   Running a paster shell with ``paster --plugin=pylons shell -c ...``.
   Useful for development?

Wrong config file path
======================

AssertionError: Config filename development.ini does not exist
  This means you forgot to give the ``--config`` or ``-c`` option to tell CKAN
  where to find your config file. (CKAN looks for a config file named
  ``development.ini`` in your current working directory by default.)

ConfigParser.MissingSectionHeaderError: File contains no section headers
  This happens if the config file that you gave with the ``-c`` or ``--config``
  option is badly formatted, or if you gave the wrong filename.

IOError: [Errno 2] No such file or directory: '...'
  This means you gave the wrong path to the ``--config`` or ``-c`` option
  (you gave a path to a file that doesn't exist).


-------------------------
Paster Commands Reference
-------------------------

The following paster commands are supported by CKAN:

================= ============================================================
celeryd           Control celery daemon.
check-po-files    Check po files for common mistakes
color             Create or remove a color scheme.
create-test-data  Create test data in the database.
dataset           Manage datasets.
datastore         Perform commands to set up the datastore.
db                Perform various tasks on the database.
front-end-build   Creates and minifies css and JavaScript files
less              Compile all root less documents into their CSS counterparts
minify            Create minified versions of the given Javascript and CSS files.
notify            Send out modification notifications.
plugin-info       Provide info on installed plugins.
profile           Code speed profiler
ratings           Manage the ratings stored in the db
rdf-export        Export active datasets as RDF.
search-index      Creates a search index for all datasets
sysadmin          Gives sysadmin rights to a named user.
tracking          Update tracking statistics.
trans             Translation helper functions
user              Manage users.
================= ============================================================


celeryd: Control celery daemon
==============================

Usage::

    celeryd <run>            - run the celery daemon
    celeryd run concurrency  - run the celery daemon with
                               argument 'concurrency'
    celeryd view             - view all tasks in the queue
    celeryd clean            - delete all tasks in the queue


check-po-files: Check po files for common mistakes
==================================================

Usage::

    check-po-files [options] [FILE] ...


color: Create or remove a color scheme
======================================

After running this command, you'll need to regenerate the css files. See :ref:`less` for details.

Usage::

    color               - creates a random color scheme
    color clear         - clears any color scheme
    color <'HEX'>       - uses as base color eg '#ff00ff' must be quoted.
    color <VALUE>       - a float between 0.0 and 1.0 used as base hue
    color <COLOR_NAME>  - html color name used for base color eg lightblue


create-test-data: Create test data
==================================

As the name suggests, this command lets you load test data when first setting up CKAN. See :ref:`create-test-data` for details.


dataset: Manage datasets
========================

Usage::

    dataset DATASET_NAME|ID            - shows dataset properties
    dataset show DATASET_NAME|ID       - shows dataset properties
    dataset list                       - lists datasets
    dataset delete [DATASET_NAME|ID]   - changes dataset state to 'deleted'
    dataset purge [DATASET_NAME|ID]    - removes dataset from db entirely


datastore: Perform commands to set up the datastore
===================================================

Make sure that the datastore URLs are set properly before you run these commands.

Usage::

    datastore set-permissions SQL_SUPER_USER

    Where:
        SQL_SUPER_USER is the name of a postgres user with sufficient
                       permissions to create new tables, users, and grant
                       and revoke new permissions.  Typically, this would
                       be the "postgres" user.


.. _paster db:

db: Manage databases
====================

Lets you initialise, upgrade, and dump the CKAN database.

Initialization
--------------

Before you can run CKAN for the first time, you need to run ``db init`` to
initialize your database:

.. parsed-literal::

 paster db init -c |production.ini|

If you forget to do this you'll see this error message in your web browser:

 503 Service Unavailable:  This site is currently off-line. Database is not
 initialised.

Cleaning
--------

You can delete everything in the CKAN database, including the tables, to start
from scratch:

.. warning::

   This will delete all data from your CKAN database!

.. parsed-literal::

 paster db clean -c |production.ini|

After cleaning the db you must do a ``db init`` or ``db load`` before CKAN will
work again.

.. _dumping and loading:

Dumping and Loading databases to/from a file
--------------------------------------------

You can 'dump' (save) the exact state of the database to a file on disk and at
a later point 'load' (restore) it again.

.. tip::

   You can also dump the database from one CKAN instance, and then load it into
   another CKAN instance on the same or another machine. This will even work if
   the CKAN instance you dumped the database from is an older version of CKAN
   than the one you load it into, the database will be automatically upgraded
   during the load command. (But you cannot load a database from a newer
   version of CKAN into an older version of CKAN.)

To export a dump of your CKAN database:

.. parsed-literal::

 paster db dump -c |production.ini| my_database_dump.sql

To load it in again, you first have to clean the database (this will delete all
data in the database!) and then load the file:

.. parsed-literal::

 paster db clean -c |production.ini|
 paster db load -c |production.ini| my_database_dump.sql

.. warning:

   The exported file is a complete backup of the database in plain text, and
   includes API keys and other user data which may be regarded as private. So
   keep it secure, like your database server.

Exporting Datasets to JSON or CSV
---------------------------------

You can export all of your CKAN site's datasets from your database to a JSON file
using the ``db simple-dump-json`` command:

.. parsed-literal::

 paster db simple-dump-json -c |production.ini| my_datasets.json

To export the datasets in CSV format instead, use ``db simple-dump-csv``:

.. parsed-literal::

 paster db simple-dump-csv -c |production.ini| my_datasets.csv

This is useful to create a simple public listing of the datasets, with no user
information. Some simple additions to the Apache config can serve the dump
files to users in a directory listing. To do this, add these lines to your
virtual Apache config file (e.g. |apache_config_file|)::

    Alias /dump/ /home/okfn/var/srvc/ckan.net/dumps/

    # Disable the mod_python handler for static files
    <Location /dump>
        SetHandler None
        Options +Indexes
    </Location>

.. warning::

   Don't serve an SQL dump of your database (created using the ``paster db
   dump`` command), as those contain private user information such as email
   addresses and API keys.

Exporting User Accounts to CSV
------------------------------

You can export all of your CKAN site's user accounts from your database to a CSV file
using the ``db user-dump-csv`` command:

.. parsed-literal::

 paster db user-dump-csv -c |production.ini| my_database_users.csv

front-end-build: Creates and minifies css and JavaScript files
==============================================================

Usage::

    front-end-build


.. _less:

less: Compile all root less documents into their CSS counterparts
=================================================================

Usage::

    less


minify: Create minified versions of the given Javascript and CSS files
======================================================================

Usage::

    paster minify [--clean] PATH

    For example:

    paster minify ckan/public/base
    paster minify ckan/public/base/css/*.css
    paster minify ckan/public/base/css/red.css

If the --clean option is provided any minified files will be removed.


notify: Send out modification notifications
===========================================

Usage::

    notify replay    - send out modification signals. In "replay" mode,
                       an update signal is sent for each dataset in the database.


plugin-info: Provide info on installed plugins
==============================================

As the name suggests, this commands shows you the installed plugins, their description, and which interfaces they implement


profile: Code speed profiler
============================

Provide a ckan url and it will make the request and record how long each function call took in a file that can be read
by runsnakerun.

Usage::

   profile URL

The result is saved in profile.data.search. To view the profile in runsnakerun::

   runsnakerun ckan.data.search.profile

You may need to install the cProfile python module.


ratings: Manage dataset ratings
===============================

Manages the ratings stored in the database, and can be used to count ratings, remove all ratings, or remove only anonymous ratings.

For example, to remove anonymous ratings from the database::

 paster --plugin=ckan ratings clean-anonymous --config=/etc/ckan/std/std.ini


rdf-export: Export datasets as RDF
==================================

This command dumps out all currently active datasets as RDF into the specified folder::

    paster rdf-export /path/to/store/output


.. _rebuild search index:

search-index: Rebuild search index
==================================

Rebuilds the search index. This is useful to prevent search indexes from getting out of sync with the main database.

For example::

 paster --plugin=ckan search-index rebuild --config=/etc/ckan/std/std.ini

This default behaviour will clear the index and rebuild it with all datasets. If you want to rebuild it for only
one dataset, you can provide a dataset name::

    paster --plugin=ckan search-index rebuild test-dataset-name --config=/etc/ckan/std/std.ini

Alternatively, you can use the `-o` or `--only-missing` option to only reindex datasets which are not
already indexed::

    paster --plugin=ckan search-index rebuild -o --config=/etc/ckan/std/std.ini

If you don't want to rebuild the whole index, but just refresh it, use the `-r` or `--refresh` option. This
won't clear the index before starting rebuilding it::

    paster --plugin=ckan search-index rebuild -r --config=/etc/ckan/std/std.ini

There is also an option available which works like the refresh option but tries to use all processes on the
computer to reindex faster::

    paster --plugin=ckan search-index rebuild_fast --config=/etc/ckan/std/std.ini

There are other search related commands, mostly useful for debugging purposes::

    search-index check                  - checks for datasets not indexed
    search-index show DATASET_NAME      - shows index of a dataset
    search-index clear [DATASET_NAME]   - clears the search index for the provided dataset or for the whole ckan instance


sysadmin: Give sysadmin rights
==============================

Gives sysadmin rights to a named user. This means the user can perform any action on any object.

For example, to make a user called 'admin' into a sysadmin::

 paster --plugin=ckan sysadmin add admin --config=/etc/ckan/std/std.ini


tracking: Update tracking statistics
====================================

Usage::

    tracking update [start_date]       - update tracking stats
    tracking export FILE [start_date]  - export tracking stats to a csv file


trans: Translation helper functions
===================================

Usage::

    trans js      - generate the javascript translations
    trans mangle  - mangle the zh_TW translations for testing


.. _paster-user:

user: Create and manage users
=============================

Lets you create, remove, list and manage users.

For example, to create a new user called 'admin'::

 paster --plugin=ckan user add admin --config=/etc/ckan/std/std.ini

To delete the 'admin' user::

 paster --plugin=ckan user remove admin --config=/etc/ckan/std/std.ini
