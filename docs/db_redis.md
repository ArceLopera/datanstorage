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

The set method adds a key-value pair to the database. In the following example, the key and value are both strings.

```python
r.set('key', 'value')
```

The get method looks up a key and returns the corresponding value.

```python
r.get('key')
```

The result is not actually a string; it is a bytearray.

For many purposes, a bytearray behaves like a string so for now we will treat it like a string and deal with differences as they arise.

The values can be integers or floating-point numbers.

```python
r.set('x', 5)
```

And Redis provides some functions that understand numbers, like incr.

```python
r.incr('x')
```

But if you get a numeric value, the result is a bytearray.

```python
value = r.get('x')
value
```

If you want to do math with it, you have to convert it back to a number.
```python
int(value)
```

If you want to set more than one value at a time, you can pass a dictionary to mset.

```python
d = dict(x=5, y='string', z=1.23)
r.mset(d)
```

```python
r.get('y')
```

```python
r.get('z')
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

### Lists

The rpush method adds new elements to the end of a list (the r indicates the right-hand side of the list).

```python
r.rpush('t', 1, 2, 3)
```

You don’t have to do anything special to create a list; if it doesn’t exist, Redis creates it.

llen returns the length of the list.

```python
r.llen('t')
```

lrange gets elements from a list. With the indices 0 and -1, it gets all of the elements.

```python
r.lrange('t', 0, -1)
```

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

### Hash

A Redis hash is similar to a Python dictionary, but just to make things confusing the nomenclature is a little different.

What we would call a “key” in a Python dictionary is called a “field” in a Redis hash.

The hset method sets a field-value pair in a hash:

```python
r.hset('h', 'field', 'value')
```

The hget method looks up a field and returns the corresponding value.

```python
r.hget('h', 'field')
```

hset can also take a Python dictionary as a parameter:

```python
d = dict(a=1, b=2, c=3)
r.hset('h', mapping=d)
```

To iterate the elements of a hash, we can use hscan_iter:

```python
for field, value in r.hscan_iter('h'):
    print(field, value)
```

The results are bytestrings for both the fields and values.

### Deleting
Before we go on, let’s clean up the database by deleting all of the key-value pairs.

```python
for key in r.keys():
    r.delete(key)
```

#Anagrams!!

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
If you are running this notebook on your own computer, you can use the following command to shut down the Redis server.

If you are running on Colab, it’s not really necessary: the Redis server will get shut down when the Colab runtime shuts down (and everything stored in it will disappear).

```python
!killall redis-server
```