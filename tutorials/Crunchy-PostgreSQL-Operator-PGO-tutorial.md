---
title: Crunchy Data PostgreSQL Operator Tutorial
description: This tutorial explains how to use Crunchy PostgreSQL Operator
---

### Introduction

The PostgreSQL Operator Client, aka pgo, is the most convenient way to interact with the PostgreSQL Operator. pgo provides many convenience methods for creating, managing, and deleting PostgreSQL clusters through a series of simple commands.

### Check the PostgreSQL DB Operator 

```execute
kubectl get pods -n pgo
```

### PGO Setup

For user to create PostgreSQL database Cluster using Crunchy PostgreSQL DB Operator

```execute
cd /home/student/projects/postgres-operator && export PGO_OPERATOR_NAMESPACE=pgo 
```

Install Client Credentials and Download the PGO Binary and Client Certificates:

```execute
PGO_CMD=kubectl ./deploy/install-bootstrap-creds.sh && PGO_CMD=kubectl ./installers/kubectl/client-setup.sh
```

Export PGO data that will be used for Cluster creation

```execute
export PATH=/home/student/.pgo/pgo:$PATH && export PGOUSER=/home/student/.pgo/pgo/pgouser && export PGO_CA_CERT=/home/student/.pgo/pgo/client.crt && export PGO_CLIENT_CERT=/home/student/.pgo/pgo/client.crt && export PGO_CLIENT_KEY=/home/student/.pgo/pgo/client.key && export PGO_APISERVER_URL=https://127.0.0.1:32443
```

Set the default namespace for PGO commands. If you skip this step you will need to add "-n pgo" to all the commands to specify the namespace

```execute
export PG_NAMESPACE=pgo

### Create a database

Create a PostgreSQL DB Cluster. This will create my-db database and the pguser database user 

```execute
pgo create cluster my-db --username pguser --password password
```

Test connectivity to the created cluster. Note the IP of the primary service that can be used to connect to this cluster.
```execute
pgo test my-db
```

To get info about the created cluster. The service field shows the IP that can be used to connect to this cluster.
```execute
pgo show cluster my-db
```

### How to use PostgreSQL client (psql) to connect and use the created cluster (database)

Connect to database using PostgreSQL client (psql). Replace the <primary-service-IP> with the correct value from 
"pgo test" or "pgo show cluster" commands
```execute
PGPASSWORD=password PGDATABASE=my-db psql -h <primary-service-IP> -U pguser
```

Using the PostgreSQL client (psql) you can create tables, insert data into them and execute queries.

Create a table
```execute
CREATE TABLE COMPANY(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT    NOT NULL,
   AGE            INT     NOT NULL,
   ADDRESS        CHAR(50),
   SALARY         REAL,
   JOIN_DATE	  DATE
);
```

Insert Values into created table

```execute
INSERT INTO COMPANY (ID,NAME,AGE,ADDRESS,SALARY,JOIN_DATE) VALUES (1, 'Paul', 32, 'California', 20000.00,'2001-07-13');
```
The following example is to insert a row; here salary column is omitted and therefore it will have the default value −
```execute
INSERT INTO COMPANY (ID,NAME,AGE,ADDRESS,JOIN_DATE) VALUES (2, 'Allen', 25, 'Texas', '2007-12-13');
```

Fetch the data from the table

```execute
SELECT * FROM company;
```

To Exit from PostgreSQL client (psql)

```execute
\q
```

### Clone a database

Clone my-db database into a new database called my-db-copy

```execute
pgo clone my-db my-db-copy
```

### Backup and restore a database

Backup my-db database. The created backup is an incremental backup. A full backup is created when the database is created.

```execute
pgo backup my-db
```

To show the backups of my-db database
```execute
pgo show backup my-db
```

To restore a database (a full restore)
```execute
pgo restore my-db
```

To restore a database at a specific point in time. For example, to restore to November 02, 2019 at 8:00am:
```execute
pgo restore my-db --pitr-target="2020-11-02 08:00:00.000000+00" --backup-opts="--type=time"
```

### Delete a database

Delete a PostgreSQL DB Cluster. This will delete my-db database

```execute
pgo delete cluster my-db
```



