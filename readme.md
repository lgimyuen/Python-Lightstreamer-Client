# Python Lightstreamer Client

## Introduction
This project is a Lightstreamer Client written in Python.

## Dependency
This package depends on Python-requests `http://www.python-requests.org/`



## Getting Started
This example is a simple stock quote demo which connect to the `DEMO` adapter_set at `https://push.lightstreamer.com:443` and subscribe to the `QUOTE_ADAPTER`. Press Enter to quit.
```python
from lightstreamer.lightstreamer import *
import pprint as pp

class Context:
    def prepare_data(self, data):
        pp.pprint(data)


context = Context()


BASE_URL = 'https://push.lightstreamer.com:443'

client = LSClient(BASE_URL, "", "", context)
client.create_session("DEMO")
client.create_stream_thread()

client.subscription("QUOTE_ADAPTER",
                    ["item1", "item2"],
                    ["last_price",
                     "time",
                     "pct_change",
                     "bid_quantity",
                     "bid",
                     "ask",
                     "ask_quantity",
                     "min",
                     "max",
                     "ref_price",
                     "open_price"])

raw_input('Hit any key to exit')

client.destroy_session()
```

### Output
```
OK
SessionId:S9c55cf407d968169T4958925
KeepaliveMillis:5000
MaxBandwidth:0.0

{'_itemNo_': 1,
 '_tableIdx_': 1,
 'ask': '3.18',
 'ask_quantity': '43000',
 'bid': '3.17',
 'bid_quantity': '32000',
 'last_price': '3.17',
 'max': '3.58',
 'min': '2.47',
 'open_price': '3.1',
 'pct_change': '4.27',
 'ref_price': '3.04',
 'time': '19:17:38'}

{'_itemNo_': 2,
 '_tableIdx_': 1,
 'ask': '15.15',
 'ask_quantity': '24000',
 'bid': '15.12',
 'bid_quantity': '61000',
 'last_price': '15.12',
 'max': '',
 'min': '',
 'open_price': '',
 'pct_change': '-6.02',
 'ref_price': '',
 'time': '19:17:45'}
```

## Usage

### Create Lightstreamer Object
Instantiate the Lightstreamer Object. No connection is made. Context is a generic object class that you can pass in. It must contain a function `context.prepare_data(data)`. Context object and the data receive from the data stream will be passed to this function.
```python
client = LSClient(url, user_id, password, context=[])
```

### Lightstreamer Create Session
Connect to `/lightstreamer/create_session.txt` and create a session. Return `True` if success, otherwise return `False`. Call `client.create_stream_thread()` after this method.
```python 
client.create_session(adapter_set)
```

### Lightstreamer Streaming Thread
Create a python thread to stream data. When data is received, pass it to `context.prepare_data(data)`.
Upon errors such as connection timeout, etc, the program will automatically call `bind_session`. If `bind_session` fails,
the program will automatically call `create_session()` and a new thread will be created.
```python 
client.create_stream_thread()
```

### Lightstreamer subscription
Subscribe to Lightstreamer through `/lightstreamer/control.txt`. Return `True` if success, otherwise return `False`.
```python
client.subscription(data_adapter, id, schema, mode="MERGE", snapshot="true", new_subscription=True)
```

### Lightstreamer bind session
Bind another Lightstreamer session through `/lightstreamer/bind_session.txt`. Return `True` if success, otherwise `False`. Call `client.create_stream_thread()` after this method.
```python
client.bind_session()
```

### Lightstreamer destroy session
Destroy the current lightstreamer session.
```python
client.destroy_session()
```