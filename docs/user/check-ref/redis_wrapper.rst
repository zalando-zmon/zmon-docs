Redis
-----

Read-only access to Redis servers is provided by the :py:func:`redis` function.


.. py:function:: redis([port=6379], [db=0])

    Returns a connection to the Redis server at :samp:`{<host>}:{<port>}`, where :samp:`{<host>}` is the value
    of the current entity's ``host`` attribute, and :samp:`{<port>}` is the given port (default ``6379``). See
    below for a list of methods provided by the returned connection object.

    :param host: Redis host.
    :type host: str

    :param password:  If set - enables authentication to the destination server with the password provided.
    :type password: str

.. note::

    You can also use ``plugin.redis.password`` plugin confirguration for zmon appliance to enabled password auth for this plugin.

Please also have a look at the `Redis documentation`_.


Methods of the Redis Connection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The object returned by the :py:func:`redis` function provides the following methods:


.. py:method:: llen(key)

    Returns the length of the list stored at `key`. If `key` does not exist, it's value is treated as if it were
    an empty list, and 0 is returned. If `key` exists but is not a list, an error is raised.

    ::

        redis().llen("prod_eventlog_queue")


.. py:method:: lrange(key, start, stop)

    Returns the elements of the list stored at `key` in the range [`start`, `stop`]. If `key` does not
    exist, it's value is treated as if it were an empty list. If `key` exists but is not a list, an
    error is raised.

    The parameters `start` and `stop` are zero-based indexes. Negative numbers are converted to indexes
    by adding the length of the list, so that ``-1`` is the last element of the list, ``-2`` the
    second-to-last element of the list, and so on.

    Indexes outside the range of the list are not an error: If both `start` and `stop` are less than 0 or
    greater than or equal to the length of the list, an empty list is returned. Otherwise, if `start` is
    less than 0, it is treated as if it were 0, and if `stop` is greater than or equal to the the length
    of the list, it is treated as if it were equal to the length of the list minus 1. If `start` is
    greater than `stop`, an empty list is returned.

    Note that this method is subtly different from Python's list slicing syntax, where ``list[start:stop]``
    returns elements in the range [`start`, `stop`).

    ::

        redis().lrange("prod_eventlog_queue", 0, 9)   # Returns *ten* elements!
        redis().lrange("prod_eventlog_queue", 0, -1)  # Returns the entire list.


.. py:method:: get(key)

    Returns the string stored at `key`. If `key` does not exist, returns ``None``. If `key` exists
    but is not a string, an error is raised.

    ::

        redis().get("example_redis_key")


.. py:method:: keys(pattern)

    Returns list of keys from Redis matching pattern.

    ::

        redis().keys("*downtime*")


.. py:method:: hget(key, field)

    Returns the value of the field `field` of the hash `key`. If `key` does not exist or does not have
    a field named `field`, returns ``None``. If `key` exists but is not a hash, an error is raised.

    ::

        redis().hget("example_hash_key", "example_field_name")


.. py:method:: hgetall(key)

    Returns a ``dict`` of all fields of the hash `key`. If `key` does not exist, returns an empty ``dict``.
    If `key` exists but is not a hash, an error is raised.

    ::

        redis().hgetall("example_hash_key")


.. py:method:: scan(cursor, [match=None], [count=None])

    Returns a ``set`` with the next cursor and the results from this scan.
    Please see the Redis documentation on how to use this function exactly: http://redis.io/commands/scan

    ::

        redis().scan(0, 'prefix*', 10)


.. py:method:: smembers(key)

    Returns members of set ``key`` in Redis.

    ::

        redis().smembers("zmon:alert:1")


.. py:method:: ttl(key)

    Return the time to live of an expiring key.

    ::

        redis().ttl('lock')

.. py:method:: scard(key)

    Return the number of elements in set ``key``

    ::

        redis().scard("example_hash_key")


.. py:method:: zcard(key)

    Return the number of elements in the sorted set ``key``

    ::

        redis().zcard("example_sorted_set_key")


.. py:method:: statistics()

    Returns a ``dict`` with general Redis statistics such as memory usage and operations/s.
    All values are extracted using the `Redis INFO command`_.

    Example result:

    .. code-block:: json

        {
            "blocked_clients": 2,
            "commands_processed_per_sec": 15946.48,
            "connected_clients": 162,
            "connected_slaves": 0,
            "connections_received_per_sec": 0.5,
            "dbsize": 27351,
            "evicted_keys_per_sec": 0.0,
            "expired_keys_per_sec": 0.0,
            "instantaneous_ops_per_sec": 29626,
            "keyspace_hits_per_sec": 1195.43,
            "keyspace_misses_per_sec": 1237.99,
            "used_memory": 50781216,
            "used_memory_rss": 63475712
        }

    Please note that the values for both `used_memory` and `used_memory_rss` are in Bytes.

.. _Redis documentation: http://redis.io/
.. _Redis INFO command: http://redis.io/commands/info
