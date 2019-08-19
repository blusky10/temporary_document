# Preparing for the Google Cloud Associate Cloud Engineer Exam

## Planning and configuring data storage

### Relational Database

Cloud SQL

* good for : web framework
* Such as : CMS, eCommerce

Cloud Spanner

* good for : RDBMS+scale, HA, HTAP
* Such as : User metadata, Ad/Fin/MarTech

### Non-relational

Cloud Datastore

* good for : Hierarchial mobile, web
* Such as : User profile, Game state

Cloud Bigtable

* good for : Heavy read, write
* Such as : AdTech

### Object

Cloud Storage

* good for : Binary or object data
* Such as : Images, media, serving

### Warehose

BigQuery

* good for : Enterprise data warehouse
* Such as : Analytics

### Cloud Strage Classes

Multi-Regional : Most frequently accessed

Regional : Accessed frequently within a region

Nearline : Accessed less than once a month

Coldline : Accessed less than once a year

## Network resources

### Global vs Regional

Global : Http(s) Load Balancing, SSL Proxy, TCP Proxy

Regional : Internal TCP/UDP Load Balancing, Network TCP/UDP Load Balancing

### External vs Internal

External : Http(s) Load Balancing, SSL Proxy, TCP Proxy, Network TCP/UDP Load Balancing

Internal : Internal TCP/UDP Load Balancing
