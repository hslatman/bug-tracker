# Bug Tracker 

A simple demo bug tracker built using API Platform (Symfony) and backed by CockroachDB

## Description

This is a simple demo application built using API Platform.
It's a minimal bug tracker with no way for tracking bugs apart from them existing or not.
Main reason for this project is to try out CockroachDB with PHP, and in particular with API Platform and Doctrine.

Three CockroachDB nodes are started and joined in a single cluster.
Currently it's an _insecure_ configuration; not suited for production usage!

One can start the application by executing _docker-compose up -d_.
This will start the three CockroachDB nodes (roach1, roach2 and roach3) and make them join the cluster.
The PHP container depends on roach1 for retrieving and storing data.
Data for each of the nodes is stored in three separate Docker volumes.

After starting the Docker container, the Swagger UI exposing the Bug Tracker API can be reached at localhost:8080 or on the IP of your docker-machine (i.e. 192.168.99.100:8080).
The CockroachDB web interface, showing the current status of the cluster and its nodes, is exposed on port 8090.

## SQL

CockroachDB supports the PostgreSQL Wire Protocol.
Unfortunately it's not 100% complete (yet), which means that some functionality does not work yet.
Doctrine is often used within Symfony applications as an ORM and offers a lot of support for SQL operations.
Due to the fact that the PostgreSQL Wire Protocol is not complete, Doctrine is not able to work to its full extent when CockroachDB is used.
Various commands, like _doctrine:schema:update_ and _doctrine:migrations:diff_ don't work at the moment.
This means that SQL to be executed needs to be figured out and executed manually (or using a script). 

The following statements should be executed:

```sql
CREATE DATABASE bt;
CREATE USER btu;
GRANT ALL ON DATABASE bt to btu;
CREATE TABLE bt.bug (id SERIAL PRIMARY KEY, title STRING, description STRING);
CREATE SEQUENCE bug_id_seq;
```

We're creating the database _bt_ and granting the user _btu_ all permissions on it.
The _bug_ table is created with a _serial_ id as its primary key and it has two fields.
The _sequence_ needs to be created in order for inserts to use the nextval() function.

Some more information about Doctrine compatiblity can be found [here](https://github.com/cockroachdb/cockroach/issues/19337) and [here](https://github.com/cockroachdb/cockroach/issues/25468).