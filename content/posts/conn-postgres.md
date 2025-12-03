+++
title = "Configuring Remote Connections with PostgreSQL"
date = 2020-02-13T19:15:15+01:00
draft = false
hideToc = true
+++

If you need to access your PostgreSQL database from another machine, you need to edit the configuration, to make the database listen to incoming connections from other hosts. Below are the steps on how to do this.

**Note**: Do not do this on your production database environment, It will open up a potential security flaw. This should be fine to do on your staging environment for testing purposes.

On the machine running PostgreSQL, locate configuration file _postgres.conf:_

```
$ su - postgres
$ psql
$ SHOW config_file;
postgres=# SHOW config_file;
                   config_file
    ------------------------------------------
     /etc/postgresql/9.6/main/postgresql.conf
    (1 row)
```

Open the file:

```
$ sudo nano /etc/postgresql/9.6/main/postgresql.conf
```

Edit the line:

```
listen_address = "localhost"
```

And append the machines IP address. So it looks like:

```
listen_address = "localhost, 192.168.0.40"
```

Note: you can get the machines IP address with _ifconfig -a_.

Now we need to edit the configuration file pg_hba.conf, you can locate the file with:

```
$ su - postgres
$ psql
$ SHOW hba_file;
```

Open the file:

```
$ sudo nano /etc/postgresql/9.6/main/pg_hba.conf
```

Add the line:

```
host    example    postgres    192.168.0.40/32    md5
```

Now restart PostgreSQL:

```
$ sudo service postgresql restart
```

#### Testing:

Let's connect to our PostgreSQL database from another machine, using Python:

```
import psycopg2

# Connect to an existing database
conn = psycopg2.connect("host='192.168.0.40' dbname='test' user='postgres' password='password'")
cur  = conn.cursor()
cur.execute("SELECT * FROM test;")
...
cur.close()
conn.close()
```

Fin
