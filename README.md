<h1 align="center">KSQLdb for Python</h1>


## Project Overview

A Python wrapper for the KSQLdb REST API


## Installation

    pip install ksqldb

## Client configuration

```python
from src.ksqldb import KSQLdbClient

client = KSQLdbClient('http://localhost:8088')
```
It can also be configured with Basic authentication:

```python
from src.ksqldb import KSQLdbClient

client = KSQLdbClient('http://localhost:8088', api_key="<KEY>", api_secret="<SECRET>")
```

## Usage

### `client.get_properties()`
Returns KSQLdb properties
```python
client.get_properties()
```

### `client.ksql(ksql_string, stream_properties=None)`
For a full list of statement options make sure to check KSQLdb API: [/ksql endpoint](https://docs.ksqldb.io/en/latest/developer-guide/ksqldb-rest-api/ksql-endpoint/)

Examples of executing KSQL statements:
```python
client.ksql("show topics;")
client.ksql("show streams;")
client.ksql("show tables;")
client.ksql("describe <STREAM_NAME> extended;")
```

### `client.query_sync(query_string, stream_properties=None, timeout=10)`
Runs a query **synchronously**.
Can't be used with `EMIT CHANGES` queries.

Examples of executing KSQL queries:
```python
client.query_sync("select * from STREAM_NAME;")

# To get data from beginning of stream use: 
client.query_sync("select * from STREAM_NAME;", stream_properties={"ksql.streams.auto.offset.reset": "earliest"})
```

### `client.query_async(query_string, stream_properties=None, timeout=10)`
Runs a query **asynchronously**.

To test this in python shell can use `python -m asyncio`
Examples of executing KSQL async queries:
```python
async for x in client.query_async("select * from STREAM_NAME emit changes;", timeout=None):
    print(x)
    
# To get data from beginning of stream use: 
async for x in client.query_async("select * from STREAM_NAME emit changes;", stream_properties={"ksql.streams.auto.offset.reset": "earliest"}, timeout=None):
    print(x)
```

### `close_query(query_id)`
Usually you don't need to close a sync query, but should be done for **async** ones.

```python
client.close_query("QUERY_ID")
```

### `inserts_stream(stream_name, rows)`
Inserts data into a stream.

```python
rows = [
    {
        "col1" : "val1",
        "col2": 2.3,
        "col3": True
    },
    {
        "col1" : "val1",
        "col2": 2.3,
        "col3": True
    },
]
client.inserts_stream("STREAM_NAME", rows)
```
