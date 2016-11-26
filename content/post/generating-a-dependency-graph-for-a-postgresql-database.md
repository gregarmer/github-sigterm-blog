+++
date        = "2010-07-09T12:00:00-04:00"
title       = "Generating a dependency graph for Postgres DB"
description = ""
tags        = [ "postgresql", "code", "python" ]
topics      = [ "code" ]
slug        = "generating-a-dependency-graph-for-a-postgresql-database"
+++

This post was mostly inspired by <a href="http://code.activestate.com/recipes/577298-plot-database-table-dependecies-for-a-mysql-databa/">this one</a>, which shows how to generate a dependency graph for a MySQL database. Here we do something similar for PostgreSQL.

This script will generate the required digraph data to pipe into <a href="http://www.graphviz.org/" target="_blank">graphviz dot</a> which will generate a visual representation of dependencies in a database schema, based on foreign key constraints.

<!--more-->

The script:

```python
from optparse import OptionParser, OptionGroup

import psycopg2
import sys


def writedeps(cursor, tbl):
    sql = """SELECT
        tc.constraint_name, tc.table_name, kcu.column_name,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
    JOIN information_schema.key_column_usage AS kcu ON
        tc.constraint_name = kcu.constraint_name
    JOIN information_schema.constraint_column_usage AS ccu ON
        ccu.constraint_name = tc.constraint_name
    WHERE constraint_type = 'FOREIGN KEY' AND tc.table_name = '%s'"""
    cursor.execute(sql % tbl)
    for row in cursor.fetchall():
        constraint, table, column, foreign_table, foreign_column = row
        print '"%s" -> "%s" [label="%s"];' % (tbl, foreign_table, constraint)


def get_tables(cursor):
    cursor.execute("SELECT tablename FROM pg_tables WHERE schemaname='public'")
    for row in cursor.fetchall():
        yield row[0]


def main():
    parser = OptionParser()

    group = OptionGroup(parser, "Database Options")
    group.add_option("--dbname", action="store", dest="dbname",
            help="The database name.")
    group.add_option("--dbhost", action="store", dest="dbhost",
            default="localhost",  help="The database host.")
    group.add_option("--dbuser", action="store", dest="dbuser",
            help="The database username.")
    group.add_option("--dbpass", action="store", dest="dbpass",
            help="The database password.")
    parser.add_option_group(group)

    (options, args) = parser.parse_args()

    if not options.dbname:
        print "Please supply a database name, see --help for more info."
        sys.exit(1)

    try:
        conn = psycopg2.connect("dbname='%s' user='%s' host='%s' password='%s'"
            % (options.dbname, options.dbuser, options.dbhost, options.dbpass))
    except psycopg2.OperationalError, e:
        print "Failed to connect to database,",
        print "perhaps you need to supply auth details:\n %s" % str(e)
        print "Use --help for more info."
        sys.exit(1)

    cursor = conn.cursor()

    print "Digraph F {\n"
    print 'ranksep=1.0; size="18.5, 15.5"; rankdir=LR;'
    for i in get_tables(cursor):
        writedeps(cursor, i)
    print "}"

    sys.exit(0)


if __name__ == "__main__":
    main()
```

You could run it as follows:

```console
python postgres-deps.py --dbname some_database | dot -Tpng > deps.png
```

<strong>Note</strong>: for other options use:

```console
python postgres-deps.py --help
```

That should spit out one of these:

![](https://media.sigterm.sh/2014/d.png)
