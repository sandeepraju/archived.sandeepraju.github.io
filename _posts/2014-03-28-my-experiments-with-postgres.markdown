---
layout: post
title: "My Experiments With Postgres"
date: 2014-03-28 18:29:00
comments: true
categories: dev database postgres sql
---

Being a Postgres user from the past couple of months, I have learnt some tricks, configs, queries that have helped me a lot. These not only save time but also makes the interaction with Postgres more pleasurable. I'll share some of my learning here...


<!-- more -->

## Writing long queries in psql like a pro

I am sure everyone agrees that writing queries (especially some long ones) in psql console is a pain in the ass. I used to generally compose those queries in a text editor of my choice and copy-paste them into the console... until I found out about the `\e` option! When you first invoke it, you will be prompted to choose the editor of your choice. Once this is done, you can invoke `\e` whenever you have to write queries and then save & close the editor to run them!

{% highlight psql %}
postgres=# \e

Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        <---- easiest
  3. /usr/bin/emacs24
  4. /usr/bin/vim.basic
  5. /usr/bin/vim.nox
  6. /usr/bin/vim.tiny

Choose 1-6 [2]: 3
{% endhighlight %}

Amazing isn't it? :D


## List all active connections to the database

Sometimes you'd want to see what all clients are accessing the database. In addition, you might want to know what query the client is running and other related details. This information can be obtained from the `pg_stat_activity`.

This is how a select query on `pg_stat_activity` looks like.

{% highlight ruby %}
postgres=# select * from pg_stat_activity;
-[ RECORD 1 ]----+--------------------------------
datid            | 11951
datname          | postgres
procpid          | 9643
usesysid         | 10
usename          | postgres
application_name | psql
client_addr      |
client_hostname  |
client_port      | -1
backend_start    | 2014-03-28 20:10:36.235241+00
xact_start       | 2014-03-28 20:10:52.49035+00
query_start      | 2014-03-28 20:10:52.49035+00
waiting          | f
current_query    | select * from pg_stat_activity;
{% endhighlight %}


As you can see, this table gives a lot of insights into the activity of the database.


## Dumping data & Loading it back

This is a general requirement where you might want to take a dump of a table or an entire database for various purposes. This is simple in Postgres.

{% highlight bash %}
# dump the entire database
$ pg_dump <database_name> > /path/to/output/file

# gzip it if the table / database is too big
$ pg_dump <database_name> | gzip -c > /path/to/output/file.gz

# load the database
$ gunzip /path/to/output/file.gz  # incase of gzip file
$ psql -d <database_name> -f /path/to/output/file
{% endhighlight %}


## Dumping the output of a query as CSV or SQL

This trick can be handy in a couple of places where we might need to dump only selective rows for further processing.

{% highlight sql %}
-- copy to sql file
COPY (SELECT * FROM table_name_here WHERE some_field>10) TO '/path/to/output/file.sql';

-- to csv
COPY (SELECT * FROM table_name_here WHERE some_field>10) TO '/path/to/output/file.sql' WITH CSV;

-- load the data to the table from the file
COPY table_name_here FROM '/path/to/output/file.sql';

-- from csv
COPY table_name_here FROM '/path/to/output/file.sql' WITH CSV;
{% endhighlight %}


## Finding size of database / table

[Surveillance Lab Wiki](http://surveillance.mcgill.ca/wiki/Postgres%20Tricks) lists three very useful commands that I have used several times over the past few months.

{% highlight sql %}
-- all database sizes
select datname, pg_size_pretty(pg_database_size(datname)) as size from pg_database order by pg_database_size(datname) desc;

-- single database size
SELECT pg_size_pretty(pg_database_size('database_name'));

-- single table size
SELECT  table_name,pg_size_pretty(pg_relation_size(table_schema || '.' || table_name)::bigint) as size
from information_schema.tables
where table_name = 'all_join_hosp';
{% endhighlight %}


## Explain & Analyze SQL queries

It is a common requirement for everyone to know how the database runs a particular query so that it can be optimized. One command that helps us lot in this regard is `EXPLAIN ANALYZE`.

{% highlight sql %}
postgres=# EXPLAIN ANALYZE select * from pg_shadow;
-[ RECORD 1 ]-------------------------------------------------------------------------------------------------------------------------------------------------------------------
QUERY PLAN | Nested Loop Left Join  (cost=0.00..9.30 rows=1 width=144) (actual time=0.017..0.018 rows=1 loops=1)
-[ RECORD 2 ]-------------------------------------------------------------------------------------------------------------------------------------------------------------------
QUERY PLAN |   ->  Seq Scan on pg_authid  (cost=0.00..1.01 rows=1 width=112) (actual time=0.007..0.007 rows=1 loops=1)
-[ RECORD 3 ]-------------------------------------------------------------------------------------------------------------------------------------------------------------------
QUERY PLAN |         Filter: rolcanlogin
-[ RECORD 4 ]-------------------------------------------------------------------------------------------------------------------------------------------------------------------
QUERY PLAN |   ->  Index Scan using pg_db_role_setting_databaseid_rol_index on pg_db_role_setting s  (cost=0.00..8.27 rows=1 width=36) (actual time=0.005..0.005 rows=0 loops=1)
-[ RECORD 5 ]-------------------------------------------------------------------------------------------------------------------------------------------------------------------
QUERY PLAN |         Index Cond: ((setdatabase = 0::oid) AND (pg_authid.oid = setrole))
-[ RECORD 6 ]-------------------------------------------------------------------------------------------------------------------------------------------------------------------
QUERY PLAN | Total runtime: 0.054 ms
{% endhighlight %}

This command dissects the query into parts and shows us what Postgres does under the hood.


## Misc

Some other posts that I found useful are,

- [WITH clause](http://www.craigkerstiens.com/2013/11/18/best-postgres-feature-youre-not-using/).
- [Changing psql prompts](http://robots.thoughtbot.com/improving-the-command-line-postgres-experience).
- [Hidden features thread on Stack Overflow](http://stackoverflow.com/questions/761327/hidden-features-of-postgresql).


### References

1. http://www.craigkerstiens.com/2013/02/13/How-I-Work-With-Postgres/
2. http://stackoverflow.com/questions/464623/how-can-you-get-the-active-users-connected-to-a-postgresql-database-via-sql
3. http://stackoverflow.com/questions/1745105/postgres-dump-of-only-parts-of-tables-for-a-dev-snapshot
4. http://stackoverflow.com/questions/1517635/save-postgres-sql-output-to-csv-file
5. http://surveillance.mcgill.ca/wiki/Postgres%20Tricks
6. http://instagram-engineering.tumblr.com/post/40781627982/handling-growth-with-postgres-5-tips-from-instagram


Happy Coding :)
