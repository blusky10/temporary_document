# Google Cloud Platform Fundamentals: Core Infrastructure

## Interconnect Options

* VPN

    Secure multi-Gbps connection over VPN tunnels

* Direct Peering

    Private connection between you and Google for your hybrid cloud workloads

* Carrier Peering

    Connection through the largest partner network of service providers

* Dedicated Interconnect

    Connect N X 10G transport circuits for private cloud traffic to Google Cloud at Google POPs

## Cloud Storage

* Object Storage

* Not a File System

### Cloud Strage Classes

* Multi-Regional : Most frequently accessed

* Regional : Accessed frequently within a region

* Nearline : Accessed less than once a month

* Coldline : Accessed less than once a year

## Cloud Big Table

* NoSQL

## Cloud SQL & Cloud Spanner

* Relational Database

## Cloud Datastore

* NoSQL

## Detail

|| Cloud DataStore | Bigtable | Cloud Store | Cloud SQL | Cloud Spanner | BigQuery |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
|Type|NoSQL|NoSQL|Blobstore|Relational SQL|Relational SQL|Relational SQL|
|Transaction|Yes|Single-row|No|Yes|Yes|No|
|Complex Query|NO|NO|NO|YES|YES|YES|
|Capacity|Terabytes|Petabytes|Petabytes|Terabytes|Petabytes|Petabytes|

## App Engine

### Standard

* Easily deploy your application

* Autoscale workloads

* Free daily quota

* Usage based pricing

### Flexible

* Web , Mobile application, continer based.

* Flexible vs Standard

|| Standard | Flexible |
|---|:---:|:---:|
|Instance Start up| milliseconds|Minutes|
|SSH access|No|Yes(althought not by default|
|Write Local disk|NO|Yes(but writes are ephemeral)|
|Support for 3rd-party binary|NO|Yes|
|Network access|Via App Engine Service|Yes|
|Pricing Network access|After free daily use, pay per instance|Pay for resource allocation per hour|

## Big Data

### Cloud Dataproc

* managed Hadoop

### Cloud Dataflow

* managed data pipeline

* ETL (extract/transform/load)

### Big Query

### Cloud Pub/Sub

* messageing Service

### Cloud Datalab
