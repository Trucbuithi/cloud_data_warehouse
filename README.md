# README

A music streaming startup, Sparkify, has grown their user base and song
database and want to move their processes and data onto the cloud. Their
data resides in S3, in a directory of JSON logs on user activity on the
app, as well as a directory with JSON metadata on the songs in their app.

This project builds an ETL pipeline that extracts their data from S3,
stages them in Redshift, and transforms data into a set of dimensional
tables for their analytics team to continue finding insights in what songs
their users are listening to. The database and ETL pipeline is tested by
running queries on the data.

## Data

### Log Data

The JSON logs on user activity have the following structure.

![log data](log-data.png)

### Song Data

Below is an example of what a single song file, TRAABJL12903CDCF1A.json
looks like.

```json
{"num_songs": 1, "artist_id": "ARJIE2Y1187B994AB7", "artist_latitude": null,
"artist_longitude": null, "artist_location": "", "artist_name": "Line Renaud",
"song_id": "SOUPIRU12A6D4FA1E1", "title": "Der Kleine Dompfaff", "duration":
152.92036, "year": 0}
```

## Schema

Star schema optimized for queries on song play analysis. This includes the following tables.

### Fact Table

| songplays | | |
|---|---|---|
songplay_id | INT IDENTITY(0,1) | PRIMARY KEY
start_time | TIMESTAMP | FOREIGN KEY
user_id | INTEGER | FOREIGN KEY
level | VARCHAR
song_id | VARCHAR | FOREIGN KEY
artist_id | VARCHAR | FOREIGN KEY
session_id | INTEGER
location | VARCHAR
user_agent | VARCHAR

### Dimension Tables

| users | | |
|---|---|---|
user_id | INTEGER | PRIMARY KEY
first_name | VARCHAR
last_name | VARCHAR
gender | VARCHAR
level | VARCHAR

| songs | | |
|---|---|---|
song_id | VARCHAR | PRIMARY KEY
title | VARCHAR
artist_id | VARCHAR | DISTKEY
year | INTEGER
duration | DECIMAL

| artists | | |
|---|---|---|
artist_id | VARCHAR | PRIMARY KEY, DISTKEY
name | VARCHAR
location | VARCHAR
latitude | DECIMAL
longitude | DECIMAL

| time | | |
|---|---|--|
start_time | TIMESTAMP | PRIMARY KEY
hour | INTEGER
day | INTEGER
week | INTEGER
month | INTEGER
year | INTEGER
weekday | INTEGER

## Usage

### Configuration

Set up a config file `dwh.cfg` that uses the following schema. Put
in the information for your Redshift cluster and IAM-Role that
can manage your cluster and read S3 buckets.

```cfg
[CLUSTER]
HOST=
DB_NAME=
DB_USER=
DB_PASSWORD=
DB_PORT=

[IAM_ROLE]
ARN=''

[S3]
LOG_DATA='s3://udacity-dend/log_data'
LOG_JSONPATH='s3://udacity-dend/log_json_path.json'
SONG_DATA='s3://udacity-dend/song_data'
```

### Loading data

When your Redshift cluster is set up run the following commands
in the console.

#### Create tables

```bash
python create_tables.py
```

#### Load data into database

```bash
python etl.py
```

#### Run test queries

```bash
python test_queries.py
```
