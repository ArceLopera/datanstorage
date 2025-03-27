# **Redis: High-Performance In-Memory Data Store**

Redis (Remote Dictionary Server) is an open-source, in-memory key-value data store known for its speed, scalability, and flexibility. It is commonly used for caching, real-time analytics, session storage, and as a message broker.

---

## **Key Features**
- **Blazing Fast Performance:** In-memory storage enables sub-millisecond read/write operations.
- **Data Persistence:** Supports both snapshotting (RDB) and append-only file persistence (AOF).
- **Data Structures:** Provides rich data types like Strings, Lists, Sets, Sorted Sets, Hashes, Bitmaps, and HyperLogLogs.
- **Pub/Sub Messaging:** Enables real-time communication between distributed applications.
- **High Availability:** Supports **replication**, **clustering**, and **sentinel mode** for fault tolerance.
- **Atomic Operations:** All commands are atomic, ensuring data consistency.
- **Lua Scripting:** Supports embedded Lua scripting for efficient processing.

---

## **Particularities of Redis**
### **In-Memory vs. Persistent Storage**
- Redis operates primarily in **RAM**, making it much faster than traditional databases like MySQL or PostgreSQL.
- Offers **optional persistence** with RDB snapshots and AOF logs.

### **Data Structures & Use Cases**
- **Strings:** Storing key-value pairs (e.g., session data, counters, user profiles).
- **Lists:** Used for message queues and task management.
- **Sets & Sorted Sets:** Ideal for leaderboards, tagging systems, and unique elements.
- **Hashes:** Storing structured objects like JSON-like documents.

### **Replication & Scaling**
- **Master-Slave Replication:** Enables read scaling with replica nodes.
- **Redis Cluster:** Distributes data across multiple nodes for horizontal scaling.
- **Sentinel Mode:** Provides automatic failover for high availability.

### **Transactions & Atomicity**
- Supports **MULTI/EXEC** transactions for executing multiple commands atomically.
- **Watch Keys:** Allows optimistic locking to prevent race conditions.

### **Cache Expiry & Eviction Policies**
- Supports TTL (Time-To-Live) for automatic expiration of keys.
- Offers multiple eviction policies like **Least Recently Used (LRU)** and **Least Frequently Used (LFU)**.

---

## **Best Use Cases for Redis**
- **Caching:** Frequently accessed data (e.g., API responses, session storage, database query results).
- **Real-Time Analytics:** Processing high-speed metrics and event tracking.
- **Message Queues:** Lightweight Pub/Sub system for real-time messaging.
- **Rate Limiting:** Controlling API request rates with atomic counters.
- **Gaming Leaderboards:** Maintaining ranked scores with Sorted Sets.
- **Machine Learning & AI:** Storing embeddings, recommendation data, and feature stores.

---

## **Redis vs. Other Databases**
| Feature | Redis |
|---------|-------|
| **Speed** | Extremely fast (in-memory) |
| **Persistence** | Optional (RDB, AOF) |
| **Data Structures** | Rich (Lists, Sets, Hashes, etc.) |
| **Replication & Scaling** | Master-Slave, Clustering |
| **Transactions** | Supports MULTI/EXEC (atomic operations) |
| **Full-Text Search** | Not natively supported |
| **Use Case** | Caching, real-time processing, message queues |

---

## **Example Usage in Python**

### **Setting Up Redis**
```python
import sys

IN_COLAB = 'google.colab' in sys.modules

if IN_COLAB:
    !pip install redis-server
    !/usr/local/lib/python*/dist-packages/redis_server/bin/redis-server --daemonize yes
else:
    !redis-server --daemonize yes
```

```python
try:
    import redis
except ImportError:
    !pip install redis
```

```python
import redis

r = redis.Redis()
```

### **Basic Operations**

The set method adds a key-value pair to the database. In the following example, the key and value are both strings.

```python
r.set('key', 'value')
print(r.get('key'))  # Output: b'value'
```

The get method looks up a key and returns the corresponding value.

The result is not actually a string; it is a bytearray.

For many purposes, a bytearray behaves like a string so for now we will treat it like a string and deal with differences as they arise.

The values can be integers or floating-point numbers.

```python
r.set('x', 5)
r.incr('x')
print(int(r.get('x')))  # Output: 6
```
And Redis provides some functions that understand numbers, like incr.
But if you get a numeric value, the result is a bytearray.
If you want to do math with it, you have to convert it back to a number, using the built-in int function.

### **Setting Multiple Values**
```python
d = dict(x=5, y='string', z=1.23)
r.mset(d)
print(r.get('y'))  # Output: b'string'
print(r.get('z'))  # Output: b'1.23'
```

If you try to store any other type in a Redis database, you get an error.

```python
from redis import DataError

t = [1, 2, 3]

try:
    r.set('t', t)
except DataError as e:
    print(e)
```

### **JSON**
We could use the repr function to create a string representation of a list, but that representation is Python-specific. It would be better to make a database that can work with any language. To do that, we can use JSON to create a string representation.

The json module provides a function dumps, that creates a language-independent representation of most Python objects.

```python
import json

t = [1, 2, 3]
s = json.dumps(t)
s
```

When we read one of these strings back, we can use loads to convert it back to a Python object.

```python
t = json.loads(s)
t
```

### **Lists**

The rpush method adds new elements to the end of a list (the r indicates the right-hand side of the list).

```python
r.rpush('t', 1, 2, 3)
print(r.lrange('t', 0, -1))  # Output: [b'1', b'2', b'3']
```
You don’t have to do anything special to create a list; if it doesn’t exist, Redis creates it.

llen returns the length of the list.

```python
r.llen('t')
```

lrange gets elements from a list. With the indices 0 and -1, it gets all of the elements.

The result is a Python list, but the elements are bytestrings.

rpop removes elements from the end of the list.

```python
r.rpop('t')
```

Note: Redis lists behave like linked lists, so you can add and remove elements from either end in constant time.

```python
r.lpush('t', -3, -2, -1)

r.lpop('t')
```

### **Hashes**

A Redis hash is similar to a Python dictionary, but just to make things confusing the nomenclature is a little different.

What we would call a “key” in a Python dictionary is called a “field” in a Redis hash.

The hset method sets a field-value pair in a hash:

```python
r.hset('h', 'field', 'value')
print(r.hget('h', 'field'))  # Output: b'value'
```
The hget method looks up a field and returns the corresponding value.

hset can also take a Python dictionary as a parameter:

```python
d = dict(a=1, b=2, c=3)
r.hset('h', mapping=d)
for field, value in r.hscan_iter('h'):
    print(field, value)
```
To iterate the elements of a hash, we can use hscan_iter. The results are bytestrings for both the fields and values.

### **Deleting Data**
```python
for key in r.keys():
    r.delete(key)
```

---

## Anagrams!!

We’ll start by solving this problem again using Python data structures; then we’ll translate it into Redis.

The following cell downloads a file that contains the list of words.

```python
from os.path import basename, exists

def download(url):
    filename = basename(url)
    if not exists(filename):
        from urllib.request import urlretrieve
        local, _ = urlretrieve(url, filename)
        print('Downloaded ' + local)
    
download('https://github.com/AllenDowney/DSIRP/raw/main/american-english')
```

And here’s a generator function that reads the words in the file and yields them one at a time.

```python
def iterate_words(filename):
    """Read lines from a file and split them into words."""
    for line in open(filename):
        for word in line.split():
            yield word.strip()
```

The “signature” of a word is a string that contains the letter of the word in sorted order. So if two words are anagrams, they have the same signature.

```python
def signature(word):
    return ''.join(sorted(word))
```

The following loop makes a dictionary of anagram lists.

```python
anagram_dict = {}
for word in iterate_words('american-english'):
    key = signature(word)
    anagram_dict.setdefault(key, []).append(word)
```

The following loop prints all anagram lists with 6 or more words

```python
for v in anagram_dict.values():
    if len(v) >= 6:
        print(len(v), v)
```

Now, to do the same thing in Redis, we have two options:

We can store the anagram lists using Redis lists, using the signatures as keys.

We can store the whole data structure in a Redis hash.

A problem with the first option is that the keys in a Redis database are like global variables. If we create a large number of keys, we are likely to run into name conflicts. We can mitigate this problem by giving each key a prefix that identifies its purpose.

The following loop implements the first option, using “Anagram” as a prefix for the keys.

```python
for word in iterate_words('american-english'):
    key = f'Anagram:{signature(word)}'
    r.rpush(key, word)
```

An advantage of this option is that it makes good use of Redis lists. A drawback is that makes many small database transactions, so it is relatively slow.

We can use keys to get a list of all keys with a given prefix.

```python
keys = r.keys('Anagram*')
len(keys)
```

Before we go on, we can delete the keys from the database like this.

```python
r.delete(*keys)
```

The second option is to compute the dictionary of anagram lists locally and then store it as a Redis hash.

The following function uses dumps to convert lists to strings that can be stored as values in a Redis hash.

```python
hash_key = 'AnagramHash'
for field, t in anagram_dict.items():
    value = json.dumps(t)
    r.hset(hash_key, field, value)
```

We can do the same thing faster if we convert all of the lists to JSON locally and store all of the field-value pairs with one hset command.

First, I’ll delete the hash we just created.

```python
r.delete(hash_key)
```

## Shut down
If you are running a notebook on your own computer, you can use the following command to shut down the Redis server.

If you are running on Colab, it’s not really necessary: the Redis server will get shut down when the Colab runtime shuts down (and everything stored in it will disappear).

```python
!killall redis-server
```

Redis is a highly efficient, in-memory data store best suited for **caching, real-time analytics, session management, and messaging systems**. Its **speed, scalability, and advanced data structures** make it a crucial component in modern web applications. However, it is not a traditional relational database, and proper use of persistence mechanisms is necessary for data durability.