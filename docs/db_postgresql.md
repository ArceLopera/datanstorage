# Reading PostgreSQL database from TensorFlow IO

<table class="tfo-notebook-buttons" align="left">
  <td>
    <a target="_blank" href="https://www.tensorflow.org/io/tutorials/postgresql"><img src="https://www.tensorflow.org/images/tf_logo_32px.png" />View on TensorFlow.org</a>
  </td>
  <td>
    <a target="_blank" href="https://colab.research.google.com/github/tensorflow/io/blob/master/docs/tutorials/postgresql.ipynb"><img src="https://www.tensorflow.org/images/colab_logo_32px.png" />Run in Google Colab</a>
  </td>
  <td>
    <a target="_blank" href="https://github.com/tensorflow/io/blob/master/docs/tutorials/postgresql.ipynb"><img src="https://www.tensorflow.org/images/GitHub-Mark-32px.png" />View source on GitHub</a>
  </td>
      <td>
    <a href="https://storage.googleapis.com/tensorflow_docs/io/docs/tutorials/postgresql.ipynb"><img src="https://www.tensorflow.org/images/download_logo_32px.png" />Download notebook</a>
  </td>
</table>

## Overview

This tutorial shows how to create `tf.data.Dataset` from a PostgreSQL database server, so that the created `Dataset` could be passed to `tf.keras` for training or inference purposes.

A SQL database is an important source of data for data scientist. As one of the most popular open source SQL database, [PostgreSQL](https://www.postgresql.org) is widely used in enterprises for storing critial and transactional data across the board. Creating `Dataset` from a PostgreSQL database server directly and pass the `Dataset` to `tf.keras` for training or inference, could greatly simplify the data pipeline and help data scientist to focus on building machine learning models.

## Setup and usage

### Install required tensorflow-io packages, and restart runtime

try:
  %tensorflow_version 2.x
except Exception:
  pass

!pip install tensorflow-io

### Install and setup PostgreSQL (optional)

**Warning: This notebook is designed to be run in a Google Colab only**. *It installs packages on the system and requires sudo access. If you want to run it in a local Jupyter notebook, please proceed with caution.*

In order to demo the usage on Google Colab you will install PostgreSQL server. The password and an empty database is also needed.

If you are not running this notebook on Google Colab, or you prefer to use an existing database, please skip the following setup and proceed to the next section.

# Install postgresql server
!sudo apt-get -y -qq update
!sudo apt-get -y -qq install postgresql
!sudo service postgresql start

# Setup a password `postgres` for username `postgres`
!sudo -u postgres psql -U postgres -c "ALTER USER postgres PASSWORD 'postgres';"

# Setup a database with name `tfio_demo` to be used
!sudo -u postgres psql -U postgres -c 'DROP DATABASE IF EXISTS tfio_demo;'
!sudo -u postgres psql -U postgres -c 'CREATE DATABASE tfio_demo;'

### Setup necessary environmental variables

The following environmental variables are based on the PostgreSQL setup in the last section. If you have a different setup or you are using an existing database, they should be changed accordingly:


%env TFIO_DEMO_DATABASE_NAME=tfio_demo
%env TFIO_DEMO_DATABASE_HOST=localhost
%env TFIO_DEMO_DATABASE_PORT=5432
%env TFIO_DEMO_DATABASE_USER=postgres
%env TFIO_DEMO_DATABASE_PASS=postgres

### Prepare data in PostgreSQL server

For demo purposes this tutorial will create a database and populate the database with some data. The data used in this tutorial is from [Air Quality Data Set](https://archive.ics.uci.edu/ml/datasets/Air+Quality), available from [UCI Machine Learning Repository](http://archive.ics.uci.edu/ml).

Below is a sneak preview of a subset of the Air Quality Data Set:

Date|Time|CO(GT)|PT08.S1(CO)|NMHC(GT)|C6H6(GT)|PT08.S2(NMHC)|NOx(GT)|PT08.S3(NOx)|NO2(GT)|PT08.S4(NO2)|PT08.S5(O3)|T|RH|AH|
----|----|------|-----------|--------|--------|-------------|----|----------|-------|------------|-----------|-|--|--|
10/03/2004|18.00.00|2,6|1360|150|11,9|1046|166|1056|113|1692|1268|13,6|48,9|0,7578|
10/03/2004|19.00.00|2|1292|112|9,4|955|103|1174|92|1559|972|13,3|47,7|0,7255|
10/03/2004|20.00.00|2,2|1402|88|9,0|939|131|1140|114|1555|1074|11,9|54,0|0,7502|
10/03/2004|21.00.00|2,2|1376|80|9,2|948|172|1092|122|1584|1203|11,0|60,0|0,7867|
10/03/2004|22.00.00|1,6|1272|51|6,5|836|131|1205|116|1490|1110|11,2|59,6|0,7888|

More information about Air Quality Data Set and UCI Machine Learning Repository are availabel in [References](#references) section.

To help simplify the data preparation, a sql version of the Air Quality Data Set has been prepared and is available as [AirQualityUCI.sql](https://github.com/tensorflow/io/blob/master/docs/tutorials/postgresql/AirQualityUCI.sql).

The statement to create the table is:
```
CREATE TABLE AirQualityUCI (
  Date DATE,
  Time TIME,
  CO REAL,
  PT08S1 INT,
  NMHC REAL,
  C6H6 REAL,
  PT08S2 INT,
  NOx REAL,
  PT08S3 INT,
  NO2 REAL,
  PT08S4 INT,
  PT08S5 INT,
  T REAL,
  RH REAL,
  AH REAL
);
```

The complete commands to create the table in database and populate the data are:

!curl -s -OL https://github.com/tensorflow/io/raw/master/docs/tutorials/postgresql/AirQualityUCI.sql

!PGPASSWORD=$TFIO_DEMO_DATABASE_PASS psql -q -h $TFIO_DEMO_DATABASE_HOST -p $TFIO_DEMO_DATABASE_PORT -U $TFIO_DEMO_DATABASE_USER -d $TFIO_DEMO_DATABASE_NAME -f AirQualityUCI.sql

### Create Dataset from PostgreSQL server and use it in TensorFlow

Create a Dataset from PostgreSQL server is as easy as calling `tfio.experimental.IODataset.from_sql` with `query` and `endpoint` arguments. The `query` is the SQL query for select columns in tables and the `endpoint` argument is the address and database name:

import os
import tensorflow_io as tfio

endpoint="postgresql://{}:{}@{}?port={}&dbname={}".format(
    os.environ['TFIO_DEMO_DATABASE_USER'],
    os.environ['TFIO_DEMO_DATABASE_PASS'],
    os.environ['TFIO_DEMO_DATABASE_HOST'],
    os.environ['TFIO_DEMO_DATABASE_PORT'],
    os.environ['TFIO_DEMO_DATABASE_NAME'],
)

dataset = tfio.experimental.IODataset.from_sql(
    query="SELECT co, pt08s1 FROM AirQualityUCI;",
    endpoint=endpoint)

print(dataset.element_spec)

As you could see from the output of `dataset.element_spec` above, the element of the created `Dataset` is a python dict object with column names of the database table as keys.
It is quite convenient to apply further operations. For example, you could select both `nox` and `no2` field of the `Dataset`, and calculate the difference:

dataset = tfio.experimental.IODataset.from_sql(
    query="SELECT nox, no2 FROM AirQualityUCI;",
    endpoint=endpoint)

dataset = dataset.map(lambda e: (e['nox'] - e['no2']))

# check only the first 20 record
dataset = dataset.take(20)

print("NOx - NO2:")
for difference in dataset:
  print(difference.numpy())

The created `Dataset` is ready to be passed to `tf.keras` directly for either training or inference purposes now.

## References

- Dua, D. and Graff, C. (2019). UCI Machine Learning Repository [http://archive.ics.uci.edu/ml]. Irvine, CA: University of California, School of Information and Computer Science.
- S. De Vito, E. Massera, M. Piga, L. Martinotto, G. Di Francia, On field calibration of an electronic nose for benzene estimation in an urban pollution monitoring scenario, Sensors and Actuators B: Chemical, Volume 129, Issue 2, 22 February 2008, Pages 750-757, ISSN 0925-4005